## centos7安装rabbitmq教程 以及 PHP开启rabbitmq扩展



#### 第一步：安装Erlang 因为rabbitMQ是Erlang语言编写的，所以我们首先需要安装Erlang

> rpm -Uvh http://www.rabbitmq.com/releases/erlang/erlang-18.1-1.el7.centos.x86_64.rpm 



#### 第二步、安装rabbitMQ-server 官网提供的安装方式：

> rpm --import https://www.rabbitmq.com/rabbitmq-signing-key-public.asc
>
>  yum install rabbitmq-server-3.5.6-1.noarch.rpm 

本人安装成功的方式： rpm -Uvh http://www.rabbitmq.com/relea...



#### 第三步、查看rabbitmq-server是否已经安装好了，能查到说明已经安装完成了。 

> rpm -qa|grep rabbitmq



#### 第四步、开启rabbit-server 

> service rabbitmq-server start



#### 第五步、关闭rabbit-server（验证命令） 

> service rabbitmq-server stop



#### 第六步、查看状态

> rabbitmqctl status



这样虽然我们已经将rabbitmq的服务正常启动了，但是我们在物理机的浏览器中输入ip:15672时，并不能连接，因为我们还没有配置维护插件和开启远程连接



#### 第七步、安装命令维护插件

> rabbitmq-plugins enable rabbitmq_management



#### 第八步、设置配置文件，并开启用户远程访问 

1、新建配置文件

> vi /etc/rabbitmq/rabbitmq.conf

```
#默认用户名
default_user = guest
#默认密码
default_pass = guest
#远程用户访问
loopback_users = none
```

2、启动rabbitmq服务

> /sbin/service rabbitmq-server start

3、查看运行

> /sbin/service rabbitmq-server status

4、开启RabbitMQ web访问

```
# rabbitmq-plugins enable rabbitmq_management 

# /sbin/service rabbitmq-server stop 

# /sbin/service rabbitmq-server start
```

5、开启端口15672，5672

```
# firewall-cmd --permanent --add-port=15672/tcp success
# firewall-cmd --permanent --add-port=5672/tcp success
# service network restart Restarting network (via systemctl)
```

6、设置开机启动

```
# chkconfig rabbitmq-server on
```

7、安装完毕web访问 浏览器输入

```
192.168.x.x:15672

默认账号密码:guest | guest
```



#### 另一种配置远程访问的方法

```
# cd /etc/rabbitmq
# cp /usr/share/doc/rabbitmq-server-3.5.6/rabbitmq.config.example /etc/rabbitmq/
# mv rabbitmq.config.example rabbitmq.config
# vi /etc/rabbitmq/rabbitmq.config
//重启 
# service rabbitmq-server restart
```



## PHP安装rabbitmq扩展需要安装rabbitmq-c

1、安装rabbitmq-c 

```
# wget -c https://github.com/alanxz/rabbitmq-c
# tar zxf rabbitmq-c-0.8.0.tar.gz
# cd rabbitmq-c-0.8.0
# ./configure --prefix=/usr/local/rabbitmq-c-0.8.0
# make && make install
```

2、 安装amqp

```
# wget -c http://pecl.php.net/get/amqp
# tar zxf amqp-1.10.2.tgz
# /usr/local/php/bin/phpize
# ./configure --with-php-config=/usr/local/php/bin/php-config --with-amqp --with-librabbitmq-dir=/usr/local/rabbitmq-c-0.8.0
# make && make install
```

3、配置amqp扩展

```
# vi /usr/local/php/etc/php.ini 
添加 extension=amqp.so
```





文章原文地址：[点击跳转](https://coffeephp.com/articles/13)