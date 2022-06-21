#### 查看系统版本号

> cat /etc/redhat-release



####  查看相关进程

grep 管道，输出指定文件内容

例如使用 ps -aux 查询进程，我们只需要知道 php 有不有运行，就可以使用管道指令『|』加入 grep 这个命令同时操作。

> ps aux | grep php



#### alias

使用alias命令可以给命令或命令序列指定自己的名称。 然后，您可以输入您的简称，然后Shell将为您执行命令或命令序列。

```
[root@qinshengfei /]# alias pf="ps -e | grep $1"
[root@qinshengfei /]# pf php
```