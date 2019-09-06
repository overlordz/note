
### 介绍

– Apache的ab命令模拟多线程并发请求，测试服务器负载压力
– 可用于Nginx、Apache、IIS等Web服务器的压力测试。

### Centos安装方式：

```
# yum -y install httpd-tools
```



### Ubuntu安装方式：

```
sudo apt-get install apache2-utils
```



#### 使用格式

```
ab [ -A auth-username:password ] [ -b windowsize ] [ -c concurrency ] [
-C cookie-name=value ] [ -d ] [ -e csv-file ] [ -f protocol ] [ -g gnu-
plot-file ] [ -h ] [ -H custom-header ] [ -i ] [ -k ] [ -n requests ] [
-p POST-file ] [ -P proxy-auth-username:password ] [ -q ] [ -r ] [ -s ]
[  -S  ] [ -t timelimit ] [ -T content-type ] [ -u PUT-file ] [ -v ver-
bosity] [ -V ] [ -w ] [ -x <table>-attributes ] [ -X proxy[:port]  ]  [
-y  <tr>-attributes  ]  [  -z  <td>-attributes  ]  [  -Z  ciphersuite ]
[http[s]://]hostname[:port]/path
```

以上看似复杂，实际简化为，

```
ab [参数] http[s]://hostname[:port]/path
```



#### 基本使用范例

```
ab -c 10 -n 10 https://www.baidu.com
```

注：
-c 参数值并发数
-n 请求的次数



####  BASIC身份验证

```
ab -A user:password https://www.baidu.com
```

注：
– 用户名和密码使用“:”分隔
– 用户名和密码可使用URL编码，亦可使用普通字符串方式表达，防止转义需要加单引号

####  定义缓冲区大小

```
ab -b 1000 https://www.baidu.com
```

#### 定义并发

```
ab -c 10 https://www.baidu.com
```

注：定义一次执行10个请求的并发量