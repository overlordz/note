> 原文地址 [learnku.com](https://learnku.com/articles/25177)，文章作者：rovast



场景
--------------

好多系统中会用到邮件系统，我们假设有一个 PHP 脚本用来发送邮件。使用 Linux cron 每分钟执行一次

> 我们暂时不引入队列系统，其实使用队列处理此方式更优。

我们得到下面的基本配置

```
* * * * * php /home/app/email.php
```



问题分析和解决
-------------------

### 如果这个邮件服务出现异常，进程僵死怎么办？

假设由于未知因素， `email.php` 脚本一直执行，没有退出。极端的情况，进入一个 while 死循环。

_这下倒好，原来说好的一分钟执行一次，现在一直死这边了，后面的脚本也不能跑了_

解决办法：

使用 timeout，假设我们设定每个脚本最多执行时间位 200 秒，超过 200 秒 就自动死掉。

```
* * * * * timeout 200 php /home/app/email.php

```

### 如果这个脚本执行时间超过 60 秒，下一分钟又会执行 `php email.php`，如果避免重复执行？

#### 这样会出现，有两个进程同时在执行 `php email.php`，那会不会出现同一个任务被执行了两次？

解决办法：

使用 flock 进行互斥控制

```
用法：
 flock [选项] <文件|目录> <命令> [<参数>...]
 flock [选项] <文件|目录> -c <命令>
 flock [选项] <文件描述符号码>

通过 shell 脚本管理文件锁。

选项：
 -s, --shared             获取共享锁
 -x, --exclusive          获取排他锁(默认)
 -u, --unlock             移除锁
 -n, --nonblock           失败而非等待
 -w, --timeout <秒>       等待限定的时间
 -E, --conflict-exit-code <数字>     冲突或超时后的退出代码
 -o, --close              运行命令前关闭文件描述符
 -c, --command <命令>      通过 shell 运行单个命令字符串
 -F, --no-fork            执行命令时不 fork
     --verbose            增加详尽程度

 -h, --help               display this help
 -V, --version            display version

```

我们用到其中的排他设置

```
* * * * * flock -xn /tmp/test.lock -c "timeout 200 php /home/app/email.php"

```

### 记录好日志

定时任务可能要记录日志呀，不然后期怎么排查

```
* * * * * flock -xn /tmp/test.lock -c "timeout 200 php /home/app/email.php >> /home/log/test.log 2>&1" 

```



总结
--------------

```
* * * * * flock -xn /tmp/test.lock -c "timeout 200 php /home/app/email.php >> /home/log/test.log 2>&1" 

```



### 番外篇 频率提升

我觉得一分钟一次频率太低，想 10s 执行一次怎么办？

```
* * * * * php /home/app/email.php >> /home/log/test.log 2>&1
* * * * * ( sleep 10 ; php /home/app/email.php >> /home/log/test.log 2>&1 )
* * * * * ( sleep 20 ; php /home/app/email.php >> /home/log/test.log 2>&1 )
* * * * * ( sleep 30 ; php /home/app/email.php >> /home/log/test.log 2>&1 )
* * * * * ( sleep 40 ; php /home/app/email.php >> /home/log/test.log 2>&1 )
* * * * * ( sleep 50 ; php /home/app/email.php >> /home/log/test.log 2>&1 )

```

### 番外篇 flock 测试

准备一个 php 脚本 `/home/rovast/Code/flock/test.php`

```
<?php

$i = 10000;
while ($i > 0) {
  echo --$i . \PHP_EOL;
  sleep(1);
}

```

执行

```
flock -xn /tmp/mytest.lock -c "timeout 30 php /home/rovast/Code/flock/test.php"

```

我们看到终端不停输出数值

```
9999
9998
9997
9996
9995
9994
9993
9992
9991
9990
```



我们再打开另外一个终端，执行

```
flock -xn /tmp/mytest.lock -c "timeout 30 php /home/rovast/Code/flock/test.php"

```

我们发现：

1.  第二次执行的没有输出（因为 flock 互斥）
2.  第一个执行的，30 秒后自动关闭进程（因为 timeout 30）
