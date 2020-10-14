##### 官方下载地址：

http://pecl.php.net/package/redis



**选择最新的下载版本**

```
# wget https://pecl.php.net/get/redis-5.1.1.tgz
```

**解压并进入源码包**

```
# tar -zvxf redis-5.1.1.tgz
# cd redis-5.1.1
```

**生成configure配置文件**

```
//  /usr/local/php ==> php安装目录
# /usr/local/php/bin/phpize
```

**编译安装**

```
# ./configure --with-php-config=/usr/local/php/bin/php-config --enable-redis
# make && make install
```

**配置php.ini**

```
// 查找配置文件路径
# php -i | grep php.ini
// 编辑
# vim /usr/local/php/lib/php.ini
// 在extension后添加
extension="/usr/local/php/lib/php/extensions/no-debug-non-zts-20151012/redis.so"
```

**重启php-fpm，并查看是否安装成功**

```
# systemctl restart php-fpm

# php -m | grep redis
```







