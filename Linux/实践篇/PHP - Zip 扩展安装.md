### 编译安装

```
// 编译php扩展的时候用的
# yum install -y php-devel 

# wget http://pecl.php.net/get/zip-1.15.4.tgz

# tar -zxvf zip-1.15.4

# cd zip-1.15.4

// 编译php扩展使用
# /usr/local/php/bin/phpize

# ./configure --with-php-config=/usr/local/php/bin/php-config

# make
// 定位错误zipconf.h：没有那个文件或目录，看来是找不到配置文件了，手动转移zipconf.h

# cp /usr/local/lib/libzip/include/zipconf.h /usr/local/include/zipconf.h

# make install

安装成功之后会有下面的信息：
Installing shared extensions:     /usr/local/php/lib/php/extensions/no-debug-non-zts-20180731/

有值证明安装成功
# php -m | grep zip
```


### 安装遇到的问题

如果再phpInfo里面看到如下内容，证明没有正确加载php.ini

> Loaded Configuration File    (none)


##### 解决办法


```
// 用strace去跟踪一下：
# strace /usr/local/php/sbin/php-fpm -i 2>1.log

# vim 1.log

打开文件发现：
    open("/usr/local/php/lib/php.ini", O_RDONLY) = -1 ENOENT (No such file or directory)

其原因是没有找到php.ini,需要把拷贝一份php.ini 到这里

# cp /usr/local/php/php.ini /usr/local/php/lib/php.ini

```