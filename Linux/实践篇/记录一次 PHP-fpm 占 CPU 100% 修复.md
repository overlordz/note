> 转自链接：https://learnku.com/articles/45409

问题描述：经业务人员反馈，前台页面返回 502 错误，上服务器查询发现，有俩个 php-fpm 占 cpu 100%, 重启 php-fpm 无效。

解决方法：
1. 通过 top 查找占 cpu 100% 的进程 id， 通过 ll /proc/PID/fd，查看进程在那个文件操作，只发现在操作日志文件，但日志文件没有数据写入。
2. 再通过 strace -p pid 查跑满 cpu 的进程 id 发现，一直报 write (xxx) 错误。
3. 再通过 df -h 发现一个磁盘数据已满，日志无法写入，导致 php-fpm 占 cpu 100%。

