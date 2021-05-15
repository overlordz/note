

**1. 前导模糊查询不能利用索引(like '%XX'或者like '%XX%')**



**2.如果是组合索引的话，如果不按照索引的顺序进行查找，比如直接使用第三个位置上的索引而忽略第一二个位置上的索引时，则会进行全表查询**



**3.条件中有or**

应尽量避免在 where 子句中使用 or 来连接条件，否则将导致引擎放弃使用索引而进行全表扫描，如：

```
select id from t where num=10 or num=20
```

可以这样查询：

```
select id from t where num=10 union all select id from t where num=20
```



**4.索引无法存储null值，所以where的判断条件如果对字段进行了null值判断，将导致数据库放弃索引而进行全表查询，如**

```
select id from t where num is null 
```

可以在num上设置默认值0，确保表中num列没有null值，然后这样查询：

```
select id from t where num=0
```



**5.应尽量避免在 where 子句中使用 != 或 <> 操作符，否则将引擎放弃使用索引而进行全表扫描。**



**6.****in 和 not in 也要慎用，否则会导致全表扫描，如：**



**7.应尽量避免在where子句中对字段进行函数操作，这将导致引擎放弃使用索引而进行全表扫描。如：**

```
select id from t where substring(name,1,3)='abc'
```



**8.****应尽量避免在 where 子句中对字段进行表达式操作，这将导致引擎放弃使用索引而进行全表扫描。如：**

```
select id from t where num/2=100
```

应改为:

```
select id from t where num=100*2
```

