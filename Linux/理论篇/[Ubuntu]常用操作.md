## 时区更改

##### 查看当前时间

> date -R

**1.运行tzselect**

> \# sudo tzselect

**在这里我们选择亚洲 Asia(4)，确认之后选择中国（China - 9)，最后选择北京(Beijing -1)**

**2.复制文件到/etc目录下 或 创建时区软链**

```
方式一：
root@ubuntu:/# sudo ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
方式二：
root@ubuntu:/# cp /usr/share/zoneinfo/Asia/Shanghai  /etc/localtime
```

#### 时间更改

```
# sudo date -s MM/DD/YY //修改日期
# sudo date -s hh:mm:ss //修改时间

在修改时间以后，修改硬件CMOS的时间,非常重要，如果没有这一步的话，后面时间还是不准
# sudo hwclock --systohc
```



## 创建用户（root）

> sudo adduser userName

#### 添加管理员权限
```
1、进入权限目录：
# sudo vim /etc/sudoers
2、添加如下内容，然后wq！保存
root    ALL=(ALL:ALL) ALL（本来就有）
koala   ALL=(ALL:ALL) ALL（新增）
```

#### 切换用户
```
切回其他用户：
# su koala
切换root：
# sudo su （输入管理员密码）
```



## PHP扩展快速安装

> apt install php版本号-扩展包名

例如：安装zip、bcmatch 扩展

$ sudo apt-get install php7.1-zip php-bcmatch

