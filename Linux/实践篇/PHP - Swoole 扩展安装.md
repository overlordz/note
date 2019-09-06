## 安装方式一：编译安装

#### PHP版本依赖

- Swoole-1.x需要 PHP-5.3.10 或更高版本

- Swoole-2.x需要 PHP-7.0.0 或更高版本

- Swoole-4.x需要 PHP-7.1.0 或更高版本

  不依赖 PHP 的 stream、sockets、pcntl、posix、sysvmsg 等扩展。PHP 只需安装最基本的扩展即可

#### 环境

用nginx + php-fpm,性能更强大,配置更方便,并且为了跟上php的步伐,也使用了比较新的php版本,我php版本7.0.26。

- centos7
- php7.0.26
- nginx/1.12.2
- php-fpm

#### 下载 swoole

> wget https://github.com/swoole/swoole-src/archive/v2.1.3.tar.gz

解压: tar -zxvf v2.1.3.tar.gz


#### 编译&安装

cd swoole-src-2.1.3/ 下,

1.使用phpize来生成php编译配置

2.使用./configure 来做编译配置检测

3.make进行编译，make install进行安装

命令执行如下：

```
[root@php7 swoole-src-2.1.3]# phpize
[root@php7 swoole-src-2.1.3]# ./configure
[root@php7 swoole-src-2.1.3]# make && make install
```

4.make install后，如果正确，会出现以下内容

```
Installing shared extensions:     /usr/local/php/lib/php/extensions/no-debug-non-zts-20151012/
Installing header files:          /usr/local/php/include/php/
```

#### 修改配置文件
要能够使用该模块，还需要在 php.ini 文件中添加该模块。

```
extension=swoole.so
```

#### 重启服务

新增php模块后，需要重新启动nginx和php-fpm服务，才会生效。

```
systemctl restart php-fpm
systemctl restart nginx
```

最后通过php -m或phpinfo()来查看是否成功



## 安装方式二：Pecl 安装

```
# pecl install swoole
# 查看php.ini位置
# php -i | grep php.ini
# 往php.ini配置文件添加
# extension=swoole.so
# 重启PHP服务
```



#### 注意事项

```
1.swoole_server只能用于php-cli环境，否则会抛出致命错误。
2.因此引入类/函数的php文件时必须要使用include_once或require_once，否会发生cannot redeclare function/class 的致命错误。
3.不要用exit()/die(),会导致worker进程退出。
4.PHP代码中如果有异常抛出，必须在回调函数中进行try/catch捕获异常，否则会导致工作进程退出。
```