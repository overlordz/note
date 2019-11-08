## binlog日志功能

### Mysql


在/etc/my.cnf文件里的[mysqld]区块添加：

> log-bin=mysql-bin

 然后重启mysql服务 



### MariaDb

修改`/etc/my.cnf.d/server.cnf` 文件

在 `[mysqld]` 下写入如下内容：

```
log_bin                   = /var/lib/mysql/bin-log
log_bin_index             = /var/lib/mysql/mysql-bin.index
expire_logs_days          = 7
server_id                 = 0002
binlog_format             = ROW
```



**解释一下：**

`log_bin = /var/lib/mysql/bin-log` ， 开启 Binlog 并写明存放日志的位置

`log_bin_index = /var/lib/mysql/mysql-bin.index` ， 指定索引文件的位置。

`expire_logs_days = 7` ，删除超出这个变量保留期之前的全部日志被删除

`server_id = 0002` ， 指定一个集群内的 MySQL 服务器 ID，如果做数据库集群那么必须全局唯一，一般来说不推荐 指定 `server_id` 等于 1。

`binlog_format = ROW`，设置方面提到过的三种 Binlog 的日志模式。

**更多选项：**

`max_binlog_size` ，binary log 最大的大小

`binlog_cache_size` ，当前的多少事务cache在内存中

`binlog_cache_disk_use` ，当前有多少事务暂存在磁盘上的，如果这个值有数值的话，就应该要注意调优了。

`max_binlog_cache_size` ，最大能有多少事务cache在内存中

`binlog_do_db`和`binlog_ingore_db` ，是一对控制对哪些数据库进行收集的选项。

`sync_binlog` ，这个值控制cache的数据commit多少次才刷到磁盘上。默认是0，也就是让数据库自己决定同步的频率。如设置成1的话，则每commit一次就会将cache的数据同步到磁盘上，这样做最安全，但是性能最差。



最后检查是否开启成功

登录 MySQL ，执行 SQL ：

```mysql
 show global variables like 'log_bin'; 
```