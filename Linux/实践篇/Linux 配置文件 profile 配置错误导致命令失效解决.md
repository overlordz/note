/etc/profile 环境变量文件配置错误后执行 source 会导致所有命令失效

解决方法：

使用绝对路径命令编辑文件，将错误配置删除

> /bin/vi /etc/profile

执行source /etc/profile会发现source无法执行，且找不到此命令的绝对路径

执行下面命令使命令生效

> export PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin

再次执行source /etc/profile恢复正常
