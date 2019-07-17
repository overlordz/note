### Redis在Ubuntu安装
```
$ sudo apt-get update

$ sudo apt-get install redis-server

$ sudo vim /etc/redis/redis.conf (修改配置文件)

#bind 127.0.0.1 这句代码加#，可以外端访问redis


```

#### 尝试启动或停止或重启redis  

1. service redis start 或者/etc/init.d/redis-server start

2. service redis stop 或者/etc/init.d/redis-server stop

3. service redis restart 或者/etc/init.d/redis-server restart

---

### Centos7 安装

#### 1.设置Redis的仓库地址
> yum install epel-release

#### 2.安装redis
```
yum install redis  修改配置文件，监听所有的IP地址

vim   /etc/redis.conf
找到下面这一行

bind 127.0.0.1
注释掉

#bind 127.0.0.1
```

#### 3.启动redis，到此完成
```
systemctl start redis

如果需要设置开机自动启动

systemctl enable redis
```

#### 查看redis版本

    redis-server –version 和 redis-server -v 
    redis-cli –version 和 redis-cli -v 

如果需要redis配置认证密码(centos7 必须设置密码)
```
1.通过配置文件进行配置
yum方式安装的redis配置文件通常在/etc/redis.conf中，打开配置文件找到

#requirepass foobared 
去掉行前的注释，并修改密码为所需的密码,保存文件

requirepass youpwd
 
重启redis完成

```