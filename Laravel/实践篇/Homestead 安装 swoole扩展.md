> 官方文档：https://wiki.swoole.com/#/environment

> 安装常见问题：https://wiki.swoole.com/#/question/install



## PECL 安装

#### 安装

```
sudo pecl install swoole
```

安装的时候会有 yes/no 之类的选项，直接 `enter` 就可以了
出现如下文字说明已经安装成功：

```shell
Build process completed successfully
Installing '/usr/include/php/20190902/ext/swoole/config.h'
Installing '/usr/lib/php/20190902/swoole.so'
install ok: channel://pecl.php.net/swoole-4.6.7
configuration option "php_ini" is not set to php.ini location
You should add "extension=swoole.so" to php.ini
```

#### 开启扩展

> 方式一：

1. 创建配置文件(php为7.4)

```
echo 'extension=swoole.so' > /etc/php/7.4/mods-available/swoole.ini
```

2. 添加软连接

```
# cli模式
sudo ln -s /etc/php/7.4/mods-available/swoole.ini /etc/php/7.4/cli/conf.d/20-swoole.ini
```

3. 重启 php-fpm

```
sudo service php7.4-fpm restart
```

4. 查看扩展

```
php -m | grep swoole
```



> 方式二：

1. 查找 php.ini 文件

```
php -i | grep php.ini
```

2. 修改 php.ini 文件

```
extension=swoole.so
```

3. 重启 php-fpm

```
sudo service php7.4-fpm restart
```



## 编译安装

下载安装包

```
wget https://github.com/swoole/swoole-src/archive/refs/tags/v4.6.7.tar.gz
```

解压文件

```
tar -zxvf v4.6.7.tar.gz
```

编译安装

```
cd swoole-src-4.6.7
/usr/bin/phpize7.4
./configure --with-php-config=/usr/bin/php-config7.4
make && make install
```

查看 php 的扩展目录

```
php -i | grep extension_dir

extension_dir => /usr/lib/php/20190902 => /usr/lib/php/20190902
```

配置扩展

```
echo 'extension=swoole.so' > /etc/php/7.4/mods-available/swoole.ini

# cli模式
sudo ln -s /etc/php/7.4/mods-available/swoole.ini /etc/php/7.4/cli/conf.d/20-swoole.ini
```

重启 php-fpm

```
sudo service php7.4-fpm restart
```

查看扩展

```
php -m | grep swoole
```