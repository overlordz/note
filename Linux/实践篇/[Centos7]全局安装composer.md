#### 1、下载composer-setup.php到当前目录

```
php -r "copy('https://install.phpcomposer.com/installer', 'composer-setup.php');" 
```

如果报出以下错误

```
PHP Warning:  copy(): SSL operation failed with code 1. OpenSSL Error messages:error:14090086:SSL routines:ssl3_get_server_certificate:certificate verify failed in Command line code on line 1
PHP Warning:  copy(): Failed to enable crypto in Command line code on line 1
PHP Warning:  copy(https://install.phpcomposer.com/installer): failed to open stream: operation failed in Command line code on line 1
```

解决办法如下：

```
下载证书 
http://curl.haxx.se/ca/cacert.pem 
wget http://curl.haxx.se/ca/cacert.pem
或者使用网页下载再上传至linux
mkdir -p /usr/local/openssl/ssl/certs/
mv cacert.pem /usr/local/openssl/ssl/certs/cacert.pem
```

修改php.ini

```
;openssl.capath=
openssl.cafile=/usr/local/openssl/ssl/certs/cacert.pem
```



#### 2、安装

```
php composer-setup.php 
```



#### 3、 将composer设置成全局

```
mv composer.phar /usr/local/bin/composer 
```



#### 4、替换成中国全量镜像

```
composer config -g repo.packagist composer https://packagist.phpcomposer.com
```

