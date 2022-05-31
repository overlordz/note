## Phpstorm + Xdebug 远程调试配置教程

### # php 自定义编译安装

####  ## 下载对应版本的 Xdebug：

xdebug官网下载地址：https://xdebug.org/download.php

你需要仔细分析和选择要下载的对应版本，否则无法调试。由于非常容易出错，建议采用下面这种简单方法：

xdebug网站提供一个自动分析你系统对应的xdebug版本的页面,网址是  https://xdebug.org/wizard.php



<img src="../../assets/linux/practice/xdebug/20190718165355.png" alt="20190718165355.png" style="zoom:80%;" />

通过复制 **phpinfo()** 输出的源码信息，放置输入框内，然后点击按钮生成 **php** 相对应的 **xdebug** 安装提示操作，然后**按照提示进行安装**

<img src="../../assets/linux/practice/xdebug/20190718165423.png" alt="20190718165423.png" style="zoom:80%;" />



安装过程出现的问题：

> 安装PHP扩展，提示checking Check for supported PHP versions... configure: error: not supported.

**处理方式**：这是php版本问题，同时我的 **php** 自定义编译安装，所以需要在配置时指定：

```
./configure -with-php-config=/usr/local/php/bin/php-config
```

检查是否安装成功： 

>  #php -m | grep xdebug



通过命令知道对应的 **php.ini** 文件

> php --ini

编辑 **php.ini** 内容

```
zend_extension="/usr/local/php/lib/php/extensions/no-debug-non-zts-20151012/xdebug.so"
xdebug.remote_autostart = 1
xdebug.remote_enable=1
xdebug.remote_connect_back=1
xdebug.idekey="PHPSTORM"
xdebug.remote_port=9001
xdebug.remote_log = /data/web/logs/xdebug.log
xdebug.remote_host="192.168.1.23"
```



### # php yum安装

本机安装的是php 7.4，同样使用yum安装xdebug：

> yum list | grep xdebug

```
[root@localhost xdebug-3.1.4]# yum list|grep xdebug
php-composer-xdebug-handler.noarch       1.4.6-1.el7                   epel     
php-pecl-xdebug.x86_64                   2.2.7-1.el7                   epel     
php54-php-pecl-xdebug.x86_64             2.4.1-1.el7.remi              remi-safe
php55-php-pecl-xdebug.x86_64             2.5.5-1.el7.remi              remi-safe
php56-php-pecl-xdebug.x86_64             2.5.5-1.el7.remi              remi-safe
php70-php-pecl-xdebug.x86_64             2.9.0-1.el7.remi              remi-safe
php71-php-pecl-xdebug.x86_64             2.9.8-1.el7.remi              remi-safe
php72-php-pecl-xdebug.x86_64             2.9.8-1.el7.remi              remi-safe
php72-php-pecl-xdebug3.x86_64            3.1.4-1.el7.remi              remi-safe
php73-php-pecl-xdebug.x86_64             2.9.8-1.el7.remi              remi-safe
php73-php-pecl-xdebug3.x86_64            3.1.4-1.el7.remi              remi-safe
php74-php-pecl-xdebug.x86_64             2.9.8-1.el7.remi              remi-safe
php74-php-pecl-xdebug3.x86_64            3.1.4-1.el7.remi              remi-safe
php80-php-pecl-xdebug3.x86_64            3.1.4-1.el7.remi              remi-safe
php81-php-pecl-xdebug3.x86_64            3.1.4-1.el7.remi              remi-safe
```

再次执行以下命令

> yum install php74-php-pecl-xdebug3

由于yum 安装的php，所以对应的扩展配置文件在：/etc/opt/remi/php74/php.d

```
-rw-r--r--  1 root root 45761 4月   4 20:26 15-xdebug.ini
-rw-r--r--  1 root root    50 12月 15 15:22 20-bcmath.ini
-rw-r--r--. 1 root root    44 12月 15 15:22 20-bz2.ini
-rw-r--r--. 1 root root    54 12月 15 15:22 20-calendar.ini
-rw-r--r--. 1 root root    48 12月 15 15:22 20-ctype.ini
-rw-r--r--. 1 root root    46 12月 15 15:22 20-curl.ini
```

zend_extension  目录在 /opt/remi/php74/root/usr/lib64/php/modules

```
-rwxr-xr-x. 1 root root  282488 12月 15 15:23 phar.so
-rwxr-xr-x. 1 root root  648384 3月  25 2021 redis.so
-rwxr-xr-x. 1 root root   58248 12月 15 15:23 simplexml.so
-rwxr-xr-x. 1 root root   95800 12月 15 15:23 sockets.so
-rwxr-xr-x. 1 root root   54376 12月 15 15:23 sqlite3.so
-rwxr-xr-x  1 root root 2972440 4月  18 15:19 swoole.so
-rwxr-xr-x. 1 root root   19840 12月 15 15:23 tokenizer.so
-rwxr-xr-x  1 root root  361424 4月   4 20:26 xdebug.so
-rwxr-xr-x. 1 root root   32992 12月 15 15:23 xmlreader.so
-rwxr-xr-x. 1 root root   53800 12月 15 15:23 xml.so
-rwxr-xr-x. 1 root root   49320 12月 15 15:23 xmlwriter.so
-rwxr-xr-x. 1 root root   32912 12月 15 15:23 xsl.so
-rwxr-xr-x. 1 root root   79488 10月 12 2021 zip.so
```

xdebug

```
;[xdebug]
zend_extension ="D:/wamp64/bin/php/php7.4.26/ext/php_xdebug.dll"
xdebug.idekey="PHPSTORM"
xdebug.client_host=localhost
; 端口ID,phpstorm 设置须一致
xdebug.client_port=9010
;开启xdebug支持，不同的mode的不同的用途，详细说明请看官方文档
xdebug.mode = develop,debug,profile,trace ;如果要多个模式一起开启，就用`,`分隔开就行
xdebug.profiler_append = 0
xdebug.profiler_output_name = cachegrind.out.%p
xdebug.start_with_request = default|yes|no|trigger ;这里与原来不同了，原来如果要开启trace或profile,用的是enable_trace,enable_profile等字段
xdebug.trigger_value=StartProfileForMe ;这里就是原来的profile_trigger_value,trace_trigger_value
xdebug.output_dir = /tmp ;输出文件路径，原来是output_profiler_dir,trace_dir分别设置,现在统一用这个设置就可以了
```







#### # PhpStorm 配置

1. **步骤一：**配置远程的 php-cli

<img src="../../assets/linux/practice/xdebug/20190718210651.png" alt="20190718210651.png" style="zoom:80%;" />



![20190718210535.png](../../assets/linux/practice/xdebug/20190718210535.png)



2. **步骤二：**配置**Debug**，点击**Validate** 验证对应的内容是否配置好

   

<img src="../../assets/linux/practice/xdebug/20190719105505.png" alt="20190719105505.png" style="zoom: 80%;" />



![20190719105812.png](../../assets/linux/practice/xdebug/20190719105812.png)



3. **步骤三：**配置 Servers（域名和端口与对应的 PHP Web 应用保持一致，用于web地址访问使用）

<img src="../../assets/linux/practice/xdebug/20190719111222.png" alt="20190719111222.png" style="zoom:90%;" />



### 问题：

#### PhpStrom Specified URL is not reachable, caused by: 'Request failed with status code 404'

该问题困惑了半天,一定要注意 Path to create validation script 的路径是项目的public目录, 这样才能验证成功, 一定要注意是public目录!!!

<img src="../../assets/linux/practice/xdebug/2022-05-25_16-44-49.png" alt="20190719111222.png" style="zoom:90%;" />





#### # 实际应用

##### 1. PHP CLI 应用

我们先来看一个本地 PHP CLI 脚本代码的调试。我们首先编写一段测试代码 `multi.php`，这段代码的作用是打印九九乘法表：

```php
<?php
// 通过二维数组生成九九乘法表
$multi = [];
for ($j = 0; $j < 9; $j++) {
    for ($i = 0; $i < 9; $i++) {
        $n1 = $i + 1;
        $n2 = $j + 1;
        if ($n1 < $n2) {  // 摒除重复的记录
            continue;
        }
        $multi[$i][$j] = sprintf("%dx%d=%d", $n2, $n1, $n1 * $n2);
    }
}

// 打印九九乘法表
foreach ($multi as $row) {
    foreach ($row as $item) {
        printf("%-8s", $item);  // 位宽为8，左对齐
    }
    printf("\n");
}
```



如果在代码执行过程中报错，或者想要了解代码执行的完整流程，可以在代码编辑区从指定位置设置断点（从任意你想要调试的位置开始）,激活Debug之后，你可以点击上图红框内的按钮进行 **断点调试**，将鼠标移到对应的按钮上会出现提示文本，比如 **Step Over** 表示跳过当前这一行，**Step Into** 表示进入函数内部等等，这里我们可以点击通过 **Step Into**（第二个按钮）逐行调试代码，在调试过程中，你可以实时看到当前作用域内变量的值

<img src="../../assets/linux/practice/xdebug/20200828145226.png" alt="20200828145226.png" style="zoom:90%;" />



这样，可以及时感知代码当前的执行状态，从而对出现问题的地方进行定位和排查原因。如果代码逻辑很复杂，变量很多，还可以通过将关心的变量添加到监控区进行查看：

<img src="../../assets/linux/practice/xdebug/20200828151823.png" alt="20200828151823.png" style="zoom:90%;" />



2. ##### PHP WEB 应用

   **前提：**PhpStorm 配置步骤三中的配置 已设置

   **实施步骤：**

   1、在 Web 请求中，需要在查看获取参数的代码前打上一个断点

   2、开启监听

   3、发送 Web 请求

   4、查看端点的数据

<img src="../../assets/linux/practice/xdebug/20200828154600.png" alt="20200828154600.png" style="zoom:90%;" />



<img src="../../assets/linux/practice/xdebug/20200828155628.png" alt="20200828155628.png" style="zoom:90%;" />





**参考文章：**

- https://xueyuanjun.com/post/21866

- http://xxlegend.com/2017/05/23/PhpStorm%20Xdebug%E8%BF%9C%E7%A8%8B%E8%B0%83%E8%AF%95%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA%E5%8E%9F%E7%90%86%E5%88%86%E6%9E%90%E5%8F%8A%E9%97%AE%E9%A2%98%E6%8E%92%E6%9F%A5/

- https://blog.csdn.net/donglynn/article/details/69808663

- <https://segmentfault.com/a/1190000014942730>

  

