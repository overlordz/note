## mysql升级到mysql 8之后 默认的加密方式发生改变

```
原代码链接Mysql8报以下错误
<b>Warning</b>:  mysqli::connect(): The server requested authentication method unknown to the client [caching_sha2_password] in <b>/data/www/php_api/cloud-api-dev/libs/Swoole/Database/MySQLi.php</b> on line <b>69</b><br />
<br />

解决方法：
将加密方式改为旧的，在配置文件my.conf中添加如下：
    [mysqld]
    default_authentication_plugin=mysql_native_password
    
使用了新的加密方式，改为旧的加密方式，而root用户也要进行相应的更改才可以，因为root用户还是新的加方式，所以使用alter语句改为重置密码来覆盖新的加密方式的密码：

    ALTER USER 'root'@'localhost'
    IDENTIFIED WITH mysql_native_password
    BY 'password';
```

> [解决链接参考](https://blog.csdn.net/qq_26819733/article/details/80794047)


## 外部软件连接Mysql，报Can't connect to MySQL server on 'x.x.x.x' (10038)


#### 解决办法：

##### 方法1、查看防火墙是不是已经开放端口，以下是centos7 firewalld为例


查看 firewalld 状态
    
    # systemctl status firewalld

开启 firewalld
    
    # systemctl start firewalld

开放端口

    // --permanent 永久生效,没有此参数重启后失效
    firewall-cmd --permanent --zone=public --add-port=3306/tcp  //使用这个命令
    firewall-cmd --permanent --zone=public --add-port=1000-2000/tcp

重新载入

    firewall-cmd --reload

查看

    firewall-cmd --zone=public --query-port=3306/tcp

删除

    firewall-cmd --zone=public --remove-port=3306/tcp --permanent

> iptables 原理一样


##### 方法2、更改Mysql配置文件

    找到bind-address = 127.0.0.1这一行
    
    改为bind-address = 0.0.0.0即可

##### 方法3、非root用户登录，授权并创建一个新用户使用

    # GRANT ALL PRIVILEGES ON *.* TO '账号'@'%' IDENTIFIED BY '密码' WITH GRANT OPTION; 
    # flush privileges;

