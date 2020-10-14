## Phpstorm + Xdebug 远程调试配置教程

####  # 下载对应版本的 Xdebug：

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



#### # PhpStorm 配置

1. **步骤一：**配置远程的 php-cli

<img src="../../assets/linux/practice/xdebug/20190718210651.png" alt="20190718210651.png" style="zoom:80%;" />



![20190718210535.png](../../assets/linux/practice/xdebug/20190718210535.png)



2. **步骤二：**配置**Debug**，点击**Validate** 验证对应的内容是否配置好

   

<img src="../../assets/linux/practice/xdebug/20190719105505.png" alt="20190719105505.png" style="zoom: 80%;" />



![20190719105812.png](../../assets/linux/practice/xdebug/20190719105812.png)



3. **步骤三：**配置 Servers（域名和端口与对应的 PHP Web 应用保持一致，用于web地址访问使用）

<img src="../../assets/linux/practice/xdebug/20190719111222.png" alt="20190719111222.png" style="zoom:90%;" />



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

  

