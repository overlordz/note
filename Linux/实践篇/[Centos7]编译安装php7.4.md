# 编译安装php7.4

> centos系统版本7.6.*，php版本7.4.26



下载地址：

> https://www.php.net/distributions/php-7.4.26.tar.gz





#### 安装遇到问题

> 编译错误提示：configure: error: Package requirements (sqlite3 > 3.7.4) were not met:

原因： 在Centos7.6 环境中配置php-7.4.19出现如上错误是因为缺少sqlite相关库

解决方法如下

```
yum -y install sqlite-devel
```



> 编译错误提示：Package requirements (oniguruma) were not met

```
yum install oniguruma-devel
```

