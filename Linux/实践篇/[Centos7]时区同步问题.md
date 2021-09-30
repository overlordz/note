## 北京时间

首先同步时间

```
yum install -y ntpdate
ntpdate -u cn.pool.ntp.org
```

然后设置中国时区（北京时间）

```
timedatectl set-timezone Asia/Shanghai
```

再次查看时间

```
date
```

重启OS

```
reboot
```

