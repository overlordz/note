查找 php-fpm 文件路径

```
ps -ef | grep php-fpm
```

得到文件所在位置:

> /usr/local/php/etc/php-fpm.conf

由于现在的 php-fpm 的配置文件都会引入 /usr/local/php/etc/php-fpm.d 目录的文件，所以我们只需要改下面的 www.conf 文件

```
vim www.conf

// 增加以下代码
request_slowlog_timeout = 1  					# 执行超过多少秒就算慢执行
slowlog = /data/web/logs/phplog/$pool.log.slow  # 慢执行日志的路径
```

然后测试修改配置是否有误：

```
/usr/local/php/sbin/php-fpm -t
```

重载 php-fpm 配置文件

```
systemctl reload php-fpm
```

新建 test 文件 

```php
<?php
echo 'test slow log';
sleep(2);
echo PHP_EOL;
```

执行测试文件

```
curl 127.0.0.1:8004/test.php
```

查看是否有慢执行日志：

```
[29-Jun-2021 16:20:39]  [pool www] pid 5277						# 基础信息
script_filename = /data/web/testCode/testone.php				# 哪一个php脚本执行慢
[0x00007fc26a2130a0] sleep() /data/web/testCode/testone.php:6	# 脚本的哪一行执行慢
```



> 参考文章：https://www.cnblogs.com/pzk7788/p/10341366.html

