#### 查看列出的php的版本信息

> **/usr/local/bin/php -v**

显示的是php5.4

> **/usr/bin/php -v**

显示的是php7

> **php -v**

显示的是php5.4



#### 环境变量中加上正确PHP版本的路径

> vim /etc/profile

```
文件末尾加上 ：
    export PATH="/usr/local/php/bin:$PATH"
    或
    PATH=$PATH:/usr/local/php/bin
    export PATH
```

> 执行命令

```
source /etc/profile
```



查看加载的php.ini路径在哪里，是否是所对应的php版本

> php -i | grep php.ini

