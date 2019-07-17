## 安装ImageMagick

下载地址：

> https://excellmedia.dl.sourceforge.net/project/imagemagick/im7-src/ImageMagick-7.0.7-25.tar.gz

```
解压

    # tar -zxvf ImageMagick-7.0.7-25.tar.gz

进入目录

    # cd ImageMagick-7.0.7-25

配置

    # ./configure

编译&安装

    # make 
    # make install
    
```

## 安装imagick

下载地址：http://pecl.php.net/package/imagick

```
条件：必须先安装ImageMagick

下载
    # wget http://pecl.php.net/get/imagick-3.4.3.tgz
    # cd imagick-3.4.3

编译php扩展使用
	# /usr/local/php/bin/phpize (或直接使用 phpize)

配置
	# ./configure --with-php-config=/usr/local/php/bin/php-config

编译&安装
    # make 
    # make install

修改php.ini 配置文件，通过phpinfo()，知道配置文件路径
    # vim /usr/local/php/lib/php.ini

添加下面内容：
    extension=imagick.so
```

