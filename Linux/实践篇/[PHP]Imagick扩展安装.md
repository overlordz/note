## 安装ImageMagick软件

下载地址：

> https://excellmedia.dl.sourceforge.net/project/imagemagick/im7-src/ImageMagick-7.0.7-25.tar.gz

解压
```shell
# tar -zxvf ImageMagick-7.0.7-25.tar.gz
```

进入目录
```shell
# cd ImageMagick-7.0.7-25
```

配置
```shell
# ./configure
```

编译&安装
```shell
# make && make install
```



## 安装imagick

下载地址：http://pecl.php.net/package/imagick

> 条件：必须先安装ImageMagick软件

下载

```shell
# wget http://pecl.php.net/get/imagick-3.4.3.tgz
# cd imagick-3.4.3
```

编译php扩展使用
```shell
# /usr/local/php/bin/phpize (或直接使用 phpize)
```
配置
```shell
# ./configure --with-php-config=/usr/local/php/bin/php-config
```

编译&安装
```
# make && make install

修改php.ini 配置文件，通过phpinfo()，知道配置文件路径
    # vim /usr/local/php/lib/php.ini

添加下面内容：
    extension=imagick.so
```



## Homestead 环境安装扩展

> 安装**imagick**的区别，因为其环境拥有**多个 php **版本

1. 下载imagick扩展，并解压

```shell
# tar -zxvf imagick-3.4.3.tgz
```

2. 进入安装目录，并使用 phpize 命令完成 php 环境的检测

```shell
//不同php版本的情况下phpize版本不同
# sudo /usr/bin/phpize7.3 
```

3. 配置

```shell
# sudo ./configure --with-php-config=/usr/bin/php-config7.3
```

4. 编译安装

```shell
# sudo make && make install
```

5. 查看扩展

```
# php -i|grep extension_dir
//返回结果
extension_dir => /usr/lib/php/20180731 => /usr/lib/php/20180731
```

6. 把 **imagick.so** 扩展模块移入 PHP 扩展目录中

```shell
# sudo cp modules/imagick.so /usr/lib/php/20180731/
```

7. 查看 **php.ini** 位置，并修改配置

```shell
# php -i | grep php.ini
# sudo vim /etc/php/7.3/cli/php.ini
```

8. 重启 **php7.3-fpm**

```shell
# sudo service php7.3-fpm restart
```

9. 查看是否安装成功

```shell
# php -m | grep imagick
```

