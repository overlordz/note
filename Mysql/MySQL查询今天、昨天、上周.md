**今天**  

> select * from 表名 where to_days(时间字段名) = to_days(now());  

**昨天**  

> SELECT * FROM 表名 WHERE TO_DAYS( NOW( ) ) - TO_DAYS( 时间字段名) <= 1  

**7天**  

> SELECT * FROM 表名 where DATE_SUB(CURDATE(), INTERVAL 7 DAY) <= date(时间字段名) 

**近30天**  

> SELECT * FROM 表名 where DATE_SUB(CURDATE(), INTERVAL 30 DAY) <= date(时间字段名) 

**本月** 

> SELECT * FROM 表名 WHERE DATE_FORMAT( 时间字段名, '%Y%m' ) = DATE_FORMAT( CURDATE( ) , '%Y%m' ) 

**上一月**  

> SELECT * FROM 表名 WHERE PERIOD_DIFF( date_format( now( ) , '%Y%m' ) , date_format( 时间字段名, '%Y%m' ) ) =1 

**查询本季度数据** 

> select * from 表名 where QUARTER(create_date)=QUARTER(now()); 

**查询上季度数据** 

> select * from 表名 where QUARTER(create_date)=QUARTER(DATE_SUB(now(),interval 1 QUARTER)); 

**查询本年数据** 

> select * from 表名 where YEAR(create_date)=YEAR(NOW());  

**查询上年数据** 

> select * from 表名 where year(create_date)=year(date_sub(now(),interval 1 year)); 

**查询当前这周的数据** 

> SELECT name,submittime FROM 表名 WHERE YEARWEEK(date_format(submittime,'%Y-%m-%d')) = YEARWEEK(now()); 

**查询上周的数据** 

> SELECT name,submittime FROM 表名 WHERE YEARWEEK(date_format(submittime,'%Y-%m-%d')) = YEARWEEK(now())-1; 

**查询当前月份的数据** 

> select name,submittime from 表名   where date_format(submittime,'%Y-%m')=date_format(now(),'%Y-%m') 

**查询距离当前现在6个月的数据** 

> select name,submittime from 表名 where submittime between date_sub(now(),interval 6 month) and now(); 

**查询上个月的数据** 

> select name,submittime from 表名 where date_format(submittime,'%Y-%m')=date_format(DATE_SUB(curdate(), INTERVAL 1 MONTH),'%Y-%m');



```
# 本周
select * from 表名 where WEEKOFYEAR(FROM_UNIXTIME(pudate,'%y-%m-%d')) = WEEKOFYEAR(now());

# 本月
select * from 表名 where MONTH (FROM_UNIXTIME(pudate, ' %y-%m-%d ' )) = MONTH (now());

# 年-月
select * from 表名 where YEAR (FROM_UNIXTIME(pudate, ' %y-%m-%d ' )) = YEAR (now()) and MONTH (FROM_UNIXTIME(pudate, ' %y-%m-%d ' )) = MONTH (now());

# 年-月-日
select * from 表名 where year(regdate)=year(now()) and month(regdate)=month(now()) and day(regdate)=day(now());
```