#### 1、下载composer-setup.php到当前目录

```
php -r "copy('https://install.phpcomposer.com/installer', 'composer-setup.php');" 
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

