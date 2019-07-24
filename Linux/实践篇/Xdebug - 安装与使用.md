## # 2019版本的Phpstorm + Xdebug 远程调试配置教程大全

####  下载对应版本的xdebug：

xdebug官网下载地址：https://xdebug.org/download.php

你需要仔细分析和选择要下载的对应版本，否则无法调试。由于非常容易出错，建议采用下面这种简单方法：

xdebug网站提供一个自动分析你系统对应的xdebug版本的页面,网址是  https://xdebug.org/wizard.php



![20190718165355.png](../../assets/linux/practice/xdebug/20190718165355.png)

通过复制 **phpinfo()** 输出的源码信息，放置输入框内，然后点击按钮生成 **php** 相对应的 **xdebug** 安装提示操作

![20190718165423.png](../../assets/linux/practice/xdebug/20190718165423.png)



安装过程出现的问题：

1、安装PHP扩展，提示checking Check for supported PHP versions... configure: error: not supported.

处理：这是php版本问题，由于我这边是自定义编译安装，

所以需要在配置时指定：	./configure -with-php-config=/usr/local/php/bin/php-config



检查是否安装成功： 

>  #  php -m | grep xdebug



通过命令知道对应的 **php.ini** 文件

> php -i | grep php.ini



编辑 **php.ini** 内容

```
zend_extension="/usr/local/php/lib/php/extensions/no-debug-non-zts-20151012/xdebug.so"
xdebug.remote_autostart = 1
xdebug.remote_enable=1
xdebug.remote_connect_back=1
xdebug.idekey="PHPSTORM"
xdebug.remote_port=9001
xdebug.remote_log = /data/web/logs/xdebug.log
```



#### PhpStorm 配置



1. **步骤一：**配置远程的 php-cli

![20190718210651.png](../../assets/linux/practice/xdebug/20190718210651.png)



![20190718210535.png](../../assets/linux/practice/xdebug/20190718210535.png)



2. **步骤二：**配置**Debug**，点击**Validate**,填写相对应的选项

   

![20190719105505.png](../../assets/linux/practice/xdebug/20190719105505.png)



![20190719105812.png](../../assets/linux/practice/xdebug/20190719105812.png)



3. **步骤二：**配置 Servers

![20190719111222.png](../../assets/linux/practice/xdebug/20190719111222.png)





参考文章：

- http://xxlegend.com/2017/05/23/PhpStorm%20Xdebug%E8%BF%9C%E7%A8%8B%E8%B0%83%E8%AF%95%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA%E5%8E%9F%E7%90%86%E5%88%86%E6%9E%90%E5%8F%8A%E9%97%AE%E9%A2%98%E6%8E%92%E6%9F%A5/

- https://blog.csdn.net/donglynn/article/details/69808663
- <https://segmentfault.com/a/1190000014942730>