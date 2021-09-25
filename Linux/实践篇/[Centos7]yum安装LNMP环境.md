## yum概要

#### yum安装LNMP流程

1. 配置yum源，本地源，线上源
2. yum -y install nginx php php-fpm mysql mysql-server 
3. 配置nginx，将.php的请求转发给php-fpm
4. 思考，找日志



#### yum版优缺点

优点：

​		方便快捷、不用考虑包依赖

缺点：
		不能按需安装、受源的限制、安装的版本也比较低



## yum源的更换

#### 一、备份 
> 进入目录下将原来的文件重命名备份

```
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo_bak
```

#### 源地址

```
sohu的源：http://mirrors.sohu.com/help/CentOS-Base-sohu.repo 
163的源：http://mirrors.163.com/.help/CentOS6-Base-163.repo 
阿里云的源：http://mirrors.aliyun.com/repo/Centos-7.repo 

更换源地址
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
```

#### 执行yum源更新命令

```
yum clean all

yum makecache

yum update
```



## yum安装NGINX

> 自centos6.9开始，nginx软件没有集成在安装盘,所以nginx源可以直接选择官网

下载地址：http://nginx.org/en/linux_packages.html
文档说明：http://nginx.org/en/linux_packages.html#RHEL-CentOS

```
创建nginx源
vi /etc/yum.repos.d/nginx.repo

内容如下：
[nginx-stable]
name=nginx stable repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=1
enabled=1
gpgkey=https://nginx.org/keys/nginx_signing.key

[nginx-mainline]
name=nginx mainline repo
baseurl=http://nginx.org/packages/mainline/centos/$releasever/$basearch/
gpgcheck=1
enabled=0
gpgkey=https://nginx.org/keys/nginx_signing.key

然后安装
sudo yum install nginx

查看状态
systemctl status nginx

设置开机启动
systemctl enable nginx
```



## yum安装PHP

#### 方式一：

1. 安装所需的依赖

    ```
    yum install epel-release yum-utils -y
    ```

2. 会对系统类型安装相应的包

   centos 7 

   ```
   yum -y install http://rpms.remirepo.net/enterprise/remi-release-7.rpm 
   ```

   centos 8

   ```
   yum -y install http://rpms.remirepo.net/enterprise/remi-release-8.rpm
   ```

3. 安装php7.4及相关扩展

  ```
  yum -y install php74-php-fpm php74-php-xml php74-php-cli php74-php-gd php74-php-json php74-php-mysql php74-php-sqlite3 php74-php-bz2 php74-php-mbstring php74-php-zip php74-php-pecl-redis4
  ```

4. 开启服务与使用

    启动

    ```
    systemctl start php74-php-fpm.service
    ```

    设置开启启动

    ```
    systemctl enable php74-php-fpm.service
    ```

    查看扩展

    ```
    php74 -m
    ```

5. 扩展使用

    查看当前的yum中有哪些php扩展

    ```
    sudo yum search php74 | more
    ```

    匹配查看某种源的包名

    ```
    sudo yum search php74 | egrep 'fpm|gd|mysql|memcache'
    ```

    安装扩展

    ```
    yum -y install php74-php-pecl-redis4
    ```

#### 方式二：

安装所需的依赖

```
yum install epel-release yum-utils -y
```

安装REMI源

```
rpm -ivh https://mirrors.tuna.tsinghua.edu.cn/remi/enterprise/remi-release-7.rpm
```

查看可以安装的PHP版本：

```
yum repolist all | grep php
```

设置默认安装的版本：

```
yum-config-manager --enable remi-php74
```

安装php

```
yum -y install php
```

查看PHP版本

```
php -v
```

查看已安装的模块

```
php -m
```

安装扩展跟方式一一样操作



## 配置php-fpm

yum安装php-fpm后的默认配置文件为 /etc/php-fpm.d/www.conf

下面介绍需要进行配置的几个参数。

> 执行用户

php-fpm执行用户和nginx一样，设定为nginx

    user = nginx

> 执行组

php-fpm执行组也和nginx一样，设定为nginx

    group = nginx

> php-fpm监听

php-fpm监听Nginx请求的方法有“**端口监听**“和“**unix socket监听**“的方法，端口监听在Nginx和php-fpm运行于不同服务器时也可使用，而unix socket监听只能用于Nginx和php-fpm在同一台服务器运行。

默认为端口监听，

    listen = 127.0.0.1:9000

nginx.conf的upstream的server设定值必须于php-fpm监听端口一致。

    http{
        ....
        upstream phpfpm {
            server 127.0.0.1:9000;
        }
    }

**unix socket监听**

unix socket监听方式比起端口监听，因为没有TCP/IP的3次握手一般来讲性能较好。

unix socket的php-fpm设定为，

    listen = /var/run/php-fpm/php-fpm.sock

nginx.conf的设定如下

    http{
        ....
        upstream phpfpm {
            server unix:/var/run/php-fpm/php-fpm.sock;
        }
    }

使用unix socket时需要设定权限，如果对socket文件没有读写权限php-fpm将无法接收Nginx处理请求。

    listen.owner = nginx
    listen.group = nginx
    listen.mode = 0666

/etc/nginx/conf.d/xxx.conf  例子:

```
server {
    listen 80;
    server_name laravel.test;
    index index.php index.html;
    root  /usr/share/nginx/html/laravel/;

    location / {
        if (!-e $request_filename) {
        	rewrite ^/(.*)$ /index.php;
        }
    }

    location ~ \.php(.*)$ {
        fastcgi_pass phpfpm;  // phpfpm =>nginx.conf 中的 upstream "phpfpm"
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}
```

## 查看yum 安装的软件路径


1. 查找php的安装包


    [root@iZbp1eem925ojwyx17ao9kZ ~]# rpm -qa|grep php
    php70w-fpm-7.0.33-1.w7.x86_64
    php70w-7.0.33-1.w7.x86_64
    php70w-cli-7.0.33-1.w7.x86_64
    php70w-common-7.0.33-1.w7.x86_64


2. 查找安装包的安装路径


    [root@localhost nginx]# rpm -ql php70w-7.0.33-1.w7.x86_64
    /etc/httpd/conf.d/php.conf
    /etc/httpd/conf.modules.d/10-php.conf
    /usr/lib64/httpd/modules/libphp7-zts.so
    /usr/lib64/httpd/modules/libphp7.so
    /usr/share/httpd/icons/php.gif
    /var/lib/php/session
    /var/lib/php/wsdlcache


​    
## 卸载yum 安装的php

#### 删除

1、如果之前编译安装了php，则进入安装位置直接删除

> 一般安装位置为/usr/local/php

2、如果之前使用yum安装的，则运行

> yum remove php php-common 

```
注意：
    如果使用删除命令之后再用
    
    #php -v
    
    还是会看到有版本信息的，必须强制删除，使用下面命令查看全部php软件包
    
    #rpm -qa|grep php
    
    提示如下：
    #php-pdo-5.1.6-27.el5_5.3
    #php-mysql-5.1.6-27.el5_5.3
    #php-xml-5.1.6-27.el5_5.3
    #php-cli-5.1.6-27.el5_5.3
    #php-common-5.1.6-27.el5_5.3
    #php-gd-5.1.6-27.el5_5.3
    
    注意卸载要先卸载没有依赖的
    pdo是mysql的依赖项；common是gd的依赖项；
    所以正确的卸载顺序是：
    # rpm -e php-mysql-5.1.6-27.el5_5.3
    # rpm -e php-pdo-5.1.6-27.el5_5.3
    # rpm -e php-xml-5.1.6-27.el5_5.3
    # rpm -e php-cli-5.1.6-27.el5_5.3
    # rpm -e php-gd-5.1.6-27.el5_5.3
    # rpm -e php-common-5.1.6-27.el5_5.3
    
    再用
    
    # php -v
    
    查看版本信息已经没有提示
```


## 安装MySql

    yum -y install mysql-server mysql