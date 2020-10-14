#### Mysql默认连接数查看

> show variables like 'max_connections';

#### 修改Mysql默认连接数

```
vim /etc/my.cnf
修改或添加：
    max_connections = 1280
    
重启Mysql
    systemctl restart mysqld
```

---

## Mysql数据库优化

#### 数据库优化的目的

##### 避免出现页面访问错误

- 由于数据库连接timeout产生页面5xx错误
- 由于慢查询造成页面无法加载
- 由于阻塞造成数据无法提交 

##### 增加数据库的稳定性

- 很多数据库问题都是由于低效的查询引起的

##### 优化用户体验

- 流畅页面的访问速度
- 良好的网站功能体验


### SQL及索引优化

##### 如何发现有问题的SQL？

使用MySql慢查日志对有效率问题的SQL进行监控

- show variables like 'slow_query_log'
> 如果没有开启则：set global slow_query_log=on;
- set global shlow_query_log_file = '/home/mysql/sql_log/mysql-slow.log'
- set global log_queries_not_using_indexes=on;
- set global long_query_time=1


##### 慢查日志的分析工具

**mysqldumpslow**

> mysqldumpslow -t 3 /home/mysql/data/mysql-slow.log | more

##### 如何分析SQL查询

    使用explain查询SQL的执行计划

> explain select customer_id,first_name from customer;

```
explain返回各列的含义
table：显示这一行的数据是关于那张表
type：这是重要的列，显示连接使用了何种类型。从最好到最差的连接类型为：const、eq_reg、ref、range、index和ALL
possible_keys：显示可能应用在这张表中的索引。如果为空，没有可能的索引。
key：实际使用的索引。如果为Null，则没有使用索引。
key_len：使用的索引的长度。在不损失精确性的情况下，长度越短越好
ref：显示索引的哪一列被使用了，如果可能的话，是一个常熟
rows：MYSQL认为必须检查的用来返回请求数据的行数
Using filesort：看到这个的时候，查询就需要优化了。MYSQL需要进行额外的步骤来发现如何对返回的行排序。它根据连接类型以及存储排序键值和匹配条件的全部行的行指针来排序全部行
Using temporary 看到这个的时候，查询需要优化了。这里，Mysql需要创建一个临时表来存储结果，这通常发生在对不同的列集进行ORDEY BY上，而不是GROUP BY上
```

##### Count()和Max()的优化方法
    查询最后支付时间--优化max()函数
```    
Max 的字段建立索引
    select max(payment_date) from payment \G
Count
    count（*）==>包含空值、count(id)==>不包含空值
    在一条SQL中同时查出2006年和2007年电影的数量--优化count()函数
    SELECT COUNT(release_year='2006' OR NULL) AS '2006电影数据',COUNT(release_year='2007' OR NULL) AS
     '2007年电影数量' FROM film；
   
```

##### 子查询的优化

    通常情况下，需要把子查询优化为join查询，但在优化时要注意关联建是否有一对多的关系，要注意重复数据。


​    
##### 如何合适的列建立索引？

```
1.在where从句，group by 从句，order by 从句，on从句中出现的列
2.索引字段越小越好
3.离散度打的列放到联合索引的签名
    查看离散度的sql语句
        select count(distinct customer_id),count(distinct staff_id) from payment;

```

##### 索引的维护及优化 --- 查找重复及冗余索引

```
SELECT a.TBLE_SCHEMA AS '数据名',a.table_name AS '表名',a.index_name AS '索引1',b.INDEX_NAME as '索引2',a.COLUMN_NAME AS '重复列名' 
FROM STATISTICS a JOIN STATISTICS b ON a.TABLE_SCHEMA=b.TABLE_SCHEMA AND a.TABLE_NAME=b.table_name AND a.SEQ_IN_INDEX=b.SEQ_IN_INDEX AND a.COLUMN_NAME = b.COLUMN_NAME WHERE A.SEQ_IN_INEX = 1 AND A.INDEX_NAME<>B.INDEX_NAME
```