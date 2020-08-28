## 安装php的stomp扩展

##### 1.stomp地址

> http://pecl.php.net/package/stomp

##### 2.下载stomp扩展包

```
#cd /home
#wget http://pecl.php.net/get/stomp-2.0.2.tgz
```

##### 3.编译安装

> phpize报 Cannot find autoconf 请安装 autoconf后再执行

> yum install m4 和 yum install autoconf

```
#tar -xzvf stomp-2.0.2.tgz
#cd stomp-2.0.2
#/usr/local/php/bin/phpize
#./configure --with-php-config=/usr/local/php/bin/php-config
其中 php-config 和 phpize 所在的目录是相同的，

如果php-config路径不是上面,比如上面我用 /usr/bin/phpize，则在这一步我用：
# ./configure --with-php-config=/usr/bin/php-config

# make && make install

返回提示如下：
	Installing shared extensions:    /usr/local/php/lib/php/extensions/no-debug-non-zts-20151012/

```

##### 3.修改php.ini

> 请通过phpinfo()查看ini文件位置

```
#vim /usr/local/php/lib/php.ini
增加行：
extension="/usr/local/php/lib/php/extensions/no-debug-non-zts-20151012/stomp.so"
重启PHP
#systemctl restart php-fpm
```

