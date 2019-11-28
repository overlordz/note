#### 系统配置优化

##### 操作系统配置优化

```
数据库是基于操作系统的，目前大多数MySql都是安装在Linux系统之上，所以对于操作系统的一些参数配置也会影响到MySql的性能，下面就列出一些常到的系统配置。

网络方面的配置，要修改/etc/sysctl.conf文件

#增加tcp支持的队列数
 net.ipv4.tcp_max_syn_backlog = 65535
#减少断开连接时，资源回收
 net.ipv4.tcp_max_tw_buckets = 8000
 net.ipv4.tcp_tw_reuse = 1
 net.ipv4.tcp_tw_recycle = 1
 net.ipv4.tcp_fin_timeout = 10
 
#打开文件数的限制，可以使用ulimit -a 查看目录的各位限制，可以修改/etc/security/limits.conf文件，增加以下内容以修改打开文件数量的限制

*soft nofile 65535
*hard nofile 65535

    除此之外最好在MySql服务器上关闭iptables,selinux等防火墙软件。
```

##### MySql配置文件 - 常用参数说明

```
#innodb_buffer_pool_size
非常重要的一个参数，用于配置Innodb的缓冲池，如果数据库中只有Innodb表，则推荐配置量为总内存的75%。

SELECT ENGINE,ROUND(SUM(data_length + index_length)/1024/1024,1) AS "TOTAL MB",FORM INFORMATION_SCHEMA.TABLES WHERE table_schema not in ("information_schema","performance_schema") GROUP BY ENGINE;

Innodb_buffer_pool_size >= Total MB

#innodb_buffer_pool_instances
MySQL5.5中新增参数，可以控制缓冲池的个数，默认情况下只有一个缓冲池。

#innodb_log_buffer_size

innodb log 缓冲的大小，由于日志最长每秒钟就会刷新所以一般不用太大。

#innodb_flush_log_at_trx_commit
关键参数，对innodb的IO效率影响很大。默认值为1，可以取0,1,2三个值，一般建议设为2，但如果数据安全性要求比较高则使用默认值1.

# innodb_read_io_threads
# innodb_write_io_threads
以上两个参数决定了Innodb读写的IO进程数，默认为4

#innodb_file_per_table
关键参数，控制Innodb每一个表使用独立的表空间，默认为OFF，也就是所有表都会建立在共享表空间中

#innodb_stats_on_metadata
决定了MySql在什么情况下回刷新innodb表的统计信息。
```

##### 第三方配置工具

    Percon Configguration Wizard
    
    https://tools.percona.com/wizard