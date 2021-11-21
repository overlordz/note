# AWK命令快速分析日志

1、总请求数

wc -l access.log |awk '{print $1}'

2、独立IP数

awk '{print $1}' access.log|sort |uniq |wc -l

3、每秒客户端请求数 TOP5

awk '{print $6}' access.log|sort|uniq -c|sort -rn|head -5

4、访问最频繁IP Top5

awk '{print $1}' access.log|sort |uniq -c |sort -nr |head -5

5、访问最频繁的URL TOP5

awk '{print $7}' access.log|sort |uniq -c |sort -nr |head -5

6、响应大于5秒的URL TOP5

awk '{if ($7 > 5){print $6}}' access.log|sort|uniq -c|sort -rn |head -5

7、HTTP状态码(非200)统计 Top5

awk '{if ($11 != 200){print $11}}' access.log|sort|uniq -c|sort -rn|head -5

8、分析请求数大于50000的源IP

cat access.log|awk '{print $NF}'|sort |uniq -c |sort -nr|awk '{if ($1 >50000){print $2}}'

 