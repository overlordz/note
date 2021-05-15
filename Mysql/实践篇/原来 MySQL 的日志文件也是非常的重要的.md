> 原文地址 \[mp.weixin.qq.com\](https://mp.weixin.qq.com/s/hWBFe6AIGv89ocR-zEKtwA)



**精选文章正文**

MySQL 中有八种日志文件，分别是：重做日志（redo log），回滚日志（undo log），二进制日志（binlog），错误日志（errorlog），慢查询日志（slow query log），一般查询日志（general log），中继日志（relay log），DDL 日志 （metadata log），他们分别都有各自的作用，而且默认情况下，服务器的日志文件都位于数据目录（datadir）中。下面来给大家详细讲解。

**一、重做日志（redo log）**
====================

作用：

确保事务的持久性。防止在发生故障的时间点，尚有脏页未写入磁盘，在重启 mysql 服务的时候，根据 redo log 进行重做，从而达到事务的持久性这一特性。

内容：

物理格式的日志，记录的是物理数据页面的修改的信息，其 redo log 是顺序写入 redo log file 的物理文件中去的。

什么时候产生：

事务开始之后就产生 redo log，redo log 的落盘并不是随着事务的提交才写入的，而是在事务的执行过程中，便开始写入 redo log 文件中。

什么时候释放：

当对应事务的脏页写入到磁盘之后，redo log 的使命也就完成了，重做日志占用的空间就可以重用（被覆盖）。

对应的物理文件：

默认情况下，对应的物理文件位于数据库的 data 目录下的 ib_logfile1&ib_logfile2

*   innodb_log_group_home_dir 指定日志文件组所在的路径，默认./ ，表示在数据库的数据目录下。

*   innodb_log_files_in_group 指定重做日志文件组中文件的数量，默认 2

关于文件的大小和数量，由以下两个参数配置：

*   innodb_log_file_size 重做日志文件的大小。

*   innodb_mirrored_log_groups 指定了日志镜像文件组的数量，默认 1

其他：

很重要一点，redo log 是什么时候写盘的？前面说了是在事物开始之后逐步写盘的。

之所以说重做日志是在事务开始之后逐步写入重做日志文件，而不一定是事务提交才写入重做日志缓存，原因就是，重做日志有一个缓存区 Innodb_log_buffer，Innodb_log_buffer 的默认大小为 8M(这里设置的 16M),Innodb 存储引擎先将重做日志写入 innodb_log_buffer 中。![](https://mmbiz.qpic.cn/mmbiz_png/QibLP1rpwH8s5N3gbVtIJWUSGiad7GM0e1aeuAhjWQ7n5s10zXkU2ceaGSSBH0fZk8QoSZ8aPQy0icxFicc070FdTg/640?wx_fmt=png)

然后会通过以下三种方式将 innodb 日志缓冲区的日志刷新到磁盘

Master Thread 每秒一次执行刷新 Innodb_log_buffer 到重做日志文件。  
每个事务提交时会将重做日志刷新到重做日志文件。  
当重做日志缓存可用空间 少于一半时，重做日志缓存被刷新到重做日志文件

由此可以看出，重做日志通过不止一种方式写入到磁盘，尤其是对于第一种方式，Innodb_log_buffer 到重做日志文件是 Master Thread 线程的定时任务。

因此重做日志的写盘，并不一定是随着事务的提交才写入重做日志文件的，而是随着事务的开始，逐步开始的。

即使某个事务还没有提交，Innodb 存储引擎仍然每秒会将重做日志缓存刷新到重做日志文件。

这一点是必须要知道的，因为这可以很好地解释再大的事务的提交（commit）的时间也是很短暂的。

**二、回滚日志（undo log）**
====================

作用：

保存了事务发生之前的数据的一个版本，可以用于回滚，同时可以提供多版本并发控制下的读（MVCC），也即非锁定读

内容：

逻辑格式的日志，在执行 undo 的时候，仅仅是将数据从逻辑上恢复至事务之前的状态，而不是从物理页面上操作实现的，这一点是不同于 redo log 的。

什么时候产生：

事务开始之前，将当前是的版本生成 undo log，undo 也会产生 redo 来保证 undo log 的可靠性

什么时候释放：

当事务提交之后，undo log 并不能立马被删除，而是放入待清理的链表，由 purge 线程判断是否由其他事务在使用 undo 段中表的上一个事务之前的版本信息，决定是否可以清理 undo log 的日志空间。

对应的物理文件：

MySQL5.6 之前，undo 表空间位于共享表空间的回滚段中，共享表空间的默认的名称是 ibdata，位于数据文件目录中。

MySQL5.6 之后，undo 表空间可以配置成独立的文件，但是提前需要在配置文件中配置，完成数据库初始化后生效且不可改变 undo log 文件的个数  
如果初始化数据库之前没有进行相关配置，那么就无法配置成独立的表空间了。

关于 MySQL5.7 之后的独立 undo 表空间配置参数如下：

*   innodb_undo_directory = /data/undospace/ –undo 独立表空间的存放目录

*   innodb_undo_logs = 128 –回滚段为 128KB

*   innodb_undo_tablespaces = 4 –指定有 4 个 undo log 文件

如果 undo 使用的共享表空间，这个共享表空间中又不仅仅是存储了 undo 的信息，共享表空间的默认为与 MySQL 的数据目录下面，其属性由参数 innodb_data_file_path 配置。![](https://mmbiz.qpic.cn/mmbiz_png/QibLP1rpwH8s5N3gbVtIJWUSGiad7GM0e1KPPdiaEpKTpyPWTwIJwZq2942YSadV8eIITAfiargwJiaCXqgbQSDMEGQ/640?wx_fmt=png)

其他：

undo 是在事务开始之前保存的被修改数据的一个版本，产生 undo 日志的时候，同样会伴随类似于保护事务持久化机制的 redolog 的产生。

默认情况下 undo 文件是保持在共享表空间的，也即 ibdatafile 文件中，当数据库中发生一些大的事务性操作的时候，要生成大量的 undo 信息，全部保存在共享表空间中的。

因此共享表空间可能会变的很大，默认情况下，也就是 undo 日志使用共享表空间的时候，被 “撑大” 的共享表空间是不会也不能自动收缩的。

因此，mysql5.7 之后的 “独立 undo 表空间” 的配置就显得很有必要了。

**三、二进制日志（binlog）**
===================

作用：

用于复制，在主从复制中，从库利用主库上的 binlog 进行重播，实现主从同步。  
用于数据库的基于时间点的还原。

内容：

逻辑格式的日志，可以简单认为就是执行过的事务中的 sql 语句。

但又不完全是 sql 语句这么简单，而是包括了执行的 sql 语句（增删改）反向的信息，也就意味着 delete 对应着 delete 本身和其反向的 insert；update 对应着 update 执行前后的版本的信息；insert 对应着 delete 和 insert 本身的信息。

在使用 mysqlbinlog 解析 binlog 之后一些都会真相大白。

因此可以基于 binlog 做到类似于 oracle 的闪回功能，其实都是依赖于 binlog 中的日志记录。

什么时候产生：

事务提交的时候，一次性将事务中的 sql 语句（一个事物可能对应多个 sql 语句）按照一定的格式记录到 binlog 中。

这里与 redo log 很明显的差异就是 redo log 并不一定是在事务提交的时候刷新到磁盘，redo log 是在事务开始之后就开始逐步写入磁盘。

因此对于事务的提交，即便是较大的事务，提交（commit）都是很快的，但是在开启了 bin_log 的情况下，对于较大事务的提交，可能会变得比较慢一些。

这是因为 binlog 是在事务提交的时候一次性写入的造成的，这些可以通过测试验证。

什么时候释放：

binlog 的默认是保持时间由参数 expire_logs_days 配置，也就是说对于非活动的日志文件，在生成时间超过 expire_logs_days 配置的天数之后，会被自动删除。![](https://mmbiz.qpic.cn/mmbiz_png/QibLP1rpwH8s5N3gbVtIJWUSGiad7GM0e1zHdH0DFsokYFf2OtYVks9k5n3PXdgyCaV8czY6qUrgghlpSSRNUibxg/640?wx_fmt=png)

对应的物理文件：

配置文件的路径为 log_bin_basename，binlog 日志文件按照指定大小，当日志文件达到指定的最大的大小之后，进行滚动更新，生成新的日志文件。

对于每个 binlog 日志文件，通过一个统一的 index 文件来组织。![](https://mmbiz.qpic.cn/mmbiz_png/QibLP1rpwH8s5N3gbVtIJWUSGiad7GM0e1HfOkZD7aLiawPOylHVLgt0XD9UybW7T0HibqSmrZgG4gichNWvoVCO88A/640?wx_fmt=png)

其他：

二进制日志的作用之一是还原数据库的，这与 redo log 很类似，很多人混淆过，但是两者有本质的不同

作用不同：redo log 是保证事务的持久性的，是事务层面的，binlog 作为还原的功能，是数据库层面的（当然也可以精确到事务层面的），虽然都有还原的意思，但是其保护数据的层次是不一样的。


内容不同：redo log 是物理日志，是数据页面的修改之后的物理记录，binlog 是逻辑日志，可以简单认为记录的就是 sql 语句


另外，两者日志产生的时间，可以释放的时间，在可释放的情况下清理机制，都是完全不同的。


恢复数据时候的效率，基于物理日志的 redo log 恢复数据的效率要高于语句逻辑日志的 binlog

关于事务提交时，redo log 和 binlog 的写入顺序，为了保证主从复制时候的主从一致（当然也包括使用 binlog 进行基于时间点还原的情况），是要严格一致的，MySQL 通过两阶段提交过程来完成事务的一致性的，也即 redo log 和 binlog 的一致性的，理论上是先写 redo log，再写 binlog，两个日志都提交成功（刷入磁盘），事务才算真正的完成。

**二进制相关的几个主要系统变量**

```
mysql> show variables like '%log_bin%';
+---------------------------------+-----------------------------+
| Variable_name                   | Value                       |
+---------------------------------+-----------------------------+
| log_bin                         | ON                          |
| log_bin_basename                | /var/lib/mysql/binlog       |
| log_bin_index                   | /var/lib/mysql/binlog.index |
| log_bin_trust_function_creators | OFF                         |
| log_bin_use_v1_row_events       | OFF                         |
| sql_log_bin                     | ON                          |
+---------------------------------+-----------------------------+
6 rows in set (0.01 sec)
```

在 MySQL 5.7 以及之前的版本中，默认没有启用二进制日志；如果想要启用，可以指定 --log-bin 选项。

从 MySQL 8.0 开始，无论是否指定 --log-bin 选项，默认开启二进制日志（系统变量 log_bin 设置为 ON）。

唯一的例外是手动执行带 --initialize 或者 --initialize-insecure 选项的 mysqld 命令初始化数据目录，此时默认不会启用二进制日志；当然也可以通过 --log-bin 选项启用二进制日志。

> 📝如果想要禁用二进制日志，可以指定 --skip-log-bin 或者 --disable-log-bin 启动选项。

系统变量 log_bin_basename 记录了二进制日志文件的路径和文件名，可以使用 --log-bin 选项进行设置。

对于 MySQL 8.0，如果没有指定 --log-bin，默认的文件名为 binlog。对于 MySQL 5.7，默认的文件名为 host_name-bin。

```
\[root@sqlhost ~\]# ls /var/lib/mysql/binlog.\* -1
/var/lib/mysql/binlog.000059
/var/lib/mysql/binlog.000060
/var/lib/mysql/binlog.000061
/var/lib/mysql/binlog.000062
/var/lib/mysql/binlog.000063
/var/lib/mysql/binlog.000064
/var/lib/mysql/binlog.000065
/var/lib/mysql/binlog.000066
/var/lib/mysql/binlog.000067
/var/lib/mysql/binlog.index
```

从文件系统可以看出，MySQL 在创建二进制日志文件时增加了一个数字扩展名。每次创建一个新的日志文件时，扩展名中的数字编号都会加 1。服务器会在以下情况创建一个新的日志文件：

*   服务器进程启动或者重启时；

*   服务器刷新日志；

*   当前日志文件的大小到达 max_binlog_size 参数的限制。

为了追踪已经使用的二进制日志文件，MySQL 还创建了一个二进制日志索引文件。默认的索引文件和二进制日志文件名相同，后缀为 “.index”。索引文件名可以通过 --log-bin-index 选项进行设置，但是千万不要在服务器运行时修改索引文件的内容。

如果客户端拥有设置 RESTRICTED SESSION 系统变量的权限，可以通过 SET sql_log_bin=OFF 语句禁用当前会话的二进制日志功能。系统变量 sql_log_bin 显示了当前的设置，默认为 ON。

日志文件中的事件格式取决于二进制日志的格式，可以通过 binlog_format 查看和设置 

*   基于语句的日志格式（statement-based logging）。以 SQL 语句为基础记录数据库的变更，MySQL 中的复制功能最初就是基于语句从主节点到从节点的传播。这种方式需要记录的日志量少，但是对于不确定性的函数（例如 now()）无法保证主从节点的一致性。通过 --binlog-format=STATEMENT 启动选项可以设置为该模式。

*   基于语句的日志格式（row-based logging），默认设置。日志中记录了每一行数据库的修改信息；如果一个 UPDATE 语句修改了 100 条记录，需要记录 100 条修改信息，所以可能产生大量的日志。通过 --binlog-format=ROW 启动选项可以设置为该模式。

*   混合日志格式（mixed logging）。默认使用基于语句的日志格式，对于不确定性的语句切换为基于行的日志格式。通过 --binlog-format=MIXED 启动选项可以设置为该模式。

**四、错误日志**
----------

MySQL 错误日志（error log）记录了 mysqld 启动和停止的相关信息，同时还记录了服务器在启动、停止以及运行期间发生的诊断消息，例如错误、警告和通知等。例如，当 mysqld 发现某个表需要执行自动检查或修复时，它会向错误日志中写入一条消息。

默认安装时，错误日志通常在配置文件（my.cnf 或者 my.ini）中 mysqld 部分的 log-error 配置项进行设置；利用 MySQL 系统变量 log_error 可以查看错误日志的文件名

```
mysql> show variables like 'log_error';
+---------------+---------------------+
| Variable_name | Value               |
+---------------+---------------------+
| log_error     | /var/log/mysqld.log |
+---------------+---------------------+
1 row in set (0.01 sec)
```

如果查询结果为 stderr，表示标准错误输出，也就是终端或控制台窗口。

从 MySQL 8.0 开始，错误日志采用了 MySQL 组件体系结构，由执行日志过滤和写入的多个组件构成，系统变量 log_error_services 配置了用于生成错误日志的组件。当 log_error 系统变量指定了默认的错误日志文件之后，日志写入器组件可能基于该值设置自己的输出文件，或者使用单独的输出文件：

*   如果 log_error 设置为 stderr，默认的错误日志将会输出到控制台；日志写入器组件 log_sink_internal、log_sink_json 以及 log_sink_test 都会输出到控制台，但是 log_sink_syseventlog 的输出目标为系统日志文件，而不是 log_error。

*   如果 log_error 设置为系统文件，默认的错误日志将会输出到该文件；日志写入器组件 log_sink_internal 和 log_sink_test 都会输出到该文件；log_error_services 中配置的多个 log_sink_json 将会使用文件名加上带编号的 .NN.json 文件：file_name.00.json、file_name.01.json 等；log_sink_syseventlog 的输出目标为系统日志文件，而不是 log_error。

  

---

五、慢查询日志
-------

MySQL 慢查询日志（slow query log）记录了执行时间超过指定阈值的 SQL 语句，可以用于监控需要执行优化的查询语句。如果启用了慢查询日志，任何执行时间超过 long_query_time 并且执行次数达到 min_examined_row_limit 次的查询语句都会被记录到慢查询日志中：

```
mysql> show variables like 'long_query_time';
+-----------------+-----------+
| Variable_name   | Value     |
+-----------------+-----------+
| long_query_time | 10.000000 |
+-----------------+-----------+
1 row in set (0.00 sec)

mysql> show variables like 'min_examined_row_limit';
+------------------------+-------+
| Variable_name          | Value |
+------------------------+-------+
| min_examined_row_limit | 0     |
+------------------------+-------+
1 row in set (0.01 sec)
```

默认情况下，执行时间超过 10 秒的任何查询都会被看作慢查询。

慢查询日志的启用和禁用由系统变量 slow_query_log 进行控制，默认禁用该功能：

```
mysql> show variables like 'slow_query_log%';
+---------------------+---------------------------------+
| Variable_name       | Value                           |
+---------------------+---------------------------------+
| slow_query_log      | OFF                             |
| slow_query_log_file | /var/lib/mysql/sqlhost-slow.log |
+---------------------+---------------------------------+
2 rows in set (0.01 sec)
```

查询结果中的 slow_query_log_file 是慢查询日志的文件名，默认为数据目录下的 _host_name-slow.log_。

与通用查询日志类似，慢查询日志既可以写入日志文件，也可以写入系统数据库中的日志表（mysql.slow_log）中；通过系统变量 log_output 进行设置，参考上文中的通用查询日志介绍。

默认情况下，管理语句不会被记录到慢查询日志，包括 ALTER TABLE、ANALYZE TABLE、CHECK TABLE、CREATE INDEX、DROP INDEX、OPTIMIZE TABLE 以及 REPAIR TABLE。如果想要记录这些操作，可以将 log_slow_admin_statements 设置为 ON：

```
mysql> show variables like 'log_slow_admin_statements';
+---------------------------+-------+
| Variable_name             | Value |
+---------------------------+-------+
| log_slow_admin_statements | OFF   |
+---------------------------+-------+
1 row in set (0.00 sec)
```

另外，不使用索引进行查找的查询也不会被记录到慢查询日志。如果想要记录这些查询，可以将 log_queries_not_using_indexes 设置为 ON：

```
mysql> show variables like 'log_queries_not_using_indexes';
+-------------------------------+-------+
| Variable_name                 | Value |
+-------------------------------+-------+
| log_queries_not_using_indexes | OFF   |
+-------------------------------+-------+
1 row in set (0.00 sec)
```

与此相关的另一个系统变量是 log_throttle_queries_not_using_indexes；如果启用了 log_queries_not_using_indexes，每分钟内执行次数超过 log_throttle_queries_not_using_indexes 的查询才会在慢查询日志中记录一条汇总信息；该参数的默认值为 0。

> 📝MySQL 通用查询日志和慢查询日志支持运行时的动态设置，包括启用、禁用、修改日志文件名和输出目标等

对于慢查询日志的分析，推荐使用 mysqldumpslow 或者 pt-query-digest 工具。

六、通用查询日志
--------

MySQL 通用查询日志（general query log）记录了 mysqld 执行的各种操作，当客户端连接或断开连接、每次提交 SQL 语句时都会记录相关的信息。当我们怀疑客户端存在问题或者想要查看客户端发送给服务器的确切请求时，通用查询日志将会非常有用。

默认情况下，通用查询日志没有启用；我们可以通过系统变量 general_log 查看和控制通用查询日志的设置：

```
mysql> show variables like 'general_log%';
+------------------+----------------------------+
| Variable_name    | Value                      |
+------------------+----------------------------+
| general_log      | OFF                        |
| general_log_file | /var/lib/mysql/sqlhost.log |
+------------------+----------------------------+
2 rows in set (0.01 sec)
```

查询结果中的 general_log_file 是通用查询日志的文件名，默认为数据目录下的 _host_name.log_。

通用查询日志和慢查询日志既可以写入日志文件，也可以写入系统数据库中的日志表中；通过系统变量 log_output 可以查看和设置：

```
mysql> show variables like 'log_output';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| log_output    | FILE  |
+---------------+-------+
1 row in set (0.01 sec)
```

FILE 表示输出到日志文件，TABLE 表示输出到日志表（分别为 mysql.general_log 和 mysql.slow_log）中，也可以设置为 TABLE, FILE；NONE 表示禁用查询日志和慢查询日志。

另外，我们还可以通过系统变量 sql_log_off 设置当前会话的通用查询日志状态，当前前提是已经启用了系统的通用查询日志功能：

```
mysql> show variables like 'sql_log_off';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| sql_log_off   | OFF   |
+---------------+-------+
1 row in set (0.01 sec)
```

OFF 表示启用日志功能；如果想要关闭当前会话的通用查询日志，可以将其设置为 ON。
------------------------------------------

  

---

七、中继日志  

---------

MySQL 中继日志（relay log）只存在于主从复制结构中的从节点上，用于保存主节点传输过来的数据变更事件，然后将这些事件应用于从节点。

中继日志与二进制日志类似，也是由一组带编号的文件组成；同时还包含了一个索引文件，记录了所有已经使用过的中继日志文件。中继日志文件默认存放在数据目录中。

```
mysql> show variables like 'relay_log%';
+---------------------------+----------------------------------------+
| Variable_name             | Value                                  |
+---------------------------+----------------------------------------+
| relay_log                 | sqlhost-relay-bin                      |
| relay_log_basename        | /var/lib/mysql/sqlhost-relay-bin       |
| relay_log_index           | /var/lib/mysql/sqlhost-relay-bin.index |
| relay_log_info_file       | relay-log.info                         |
| relay_log_info_repository | TABLE                                  |
| relay_log_purge           | ON                                     |
| relay_log_recovery        | OFF                                    |
| relay_log_space_limit     | 0                                      |
+---------------------------+----------------------------------------+
8 rows in set (0.01 sec)
```

其中，relay_log 和 relay_log_index 分别对应为中继日志的文件名和索引文件名。  

中继日志文件的格式和二进制日志文件的格式相同，因此也可以使用 mysqlbinlog 工具进行读取。

**八、DDL 日志**
------------

MySQL DDL 日志，也称为元数据日志（metadata log）记录了数据定义语言执行的元数据操作，例如 DROP TABLE 或者 ALTER TABLE。

如果元数据操作的过程中发生故障，MySQL 使用该日志进行恢复。当我们执行 DROP TABLE t1, t2 命令时，需要确保 t1 和 t2 一起被删除 ，而且每个表都被完整地删除。另一个示例是 ALTER TABLE t3 DROP PARTITION p2 语句，必须确保分区被完整删除并且分区的定义从 t3 的分区列表中删除。

对于 MySQL 5.7 以及之前的版本，元数据操作的记录日志位于数据目录下的 ddl_log.log 文件。该文件是一个二进制文件，不支持手动修改，也没有相关的配置选项和变量。

对于 MySQL 8.0，DDL 日志存储在 mysql.innodb_ddl_log 数据字典中。这是一个隐藏的数据字典表，不支持直接查询；可以通过设置系统变量 innodb_print_ddl_logs （默认为 OFF）将 DDL 日志打印到标准错误输出 strderr。