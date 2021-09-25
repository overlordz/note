
### 介绍

– Apache的ab命令模拟多线程并发请求，测试服务器负载压力
– 可用于Nginx、Apache、IIS等Web服务器的压力测试。

### 安装方式

#### Centos：

```
# 查看软件
yum search httpd | grep tools
yum -y install httpd-tools
```

### Ubuntu：

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
ab -c 10 -n 10 https://www.baidu.com/
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









### 开始测试

#### 输入命令

> ab -n 100 -c 10 http://test.com/

其中－n表示请求数，－c表示并发数

#### 测试结果分析

上面的命令运行完毕后就出来测试报告了

![image](https://upload-images.jianshu.io/upload_images/539132-78416e4b8ec72659.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/609)


这段展示的是web服务器的信息，可以看到服务器采用的是nginx，域名是wan.bigertech.com，端口是80

![image](https://upload-images.jianshu.io/upload_images/539132-b3dc1723132b9864.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/347)

> 服务器信息

这段是关于请求的文档的相关信息，所在位置“/”，文档的大小为338436 bytes（此为http响应的正文长度）

![image](https://upload-images.jianshu.io/upload_images/539132-6121e41e28e3d7d1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/358)

> 文档信息

这段展示了压力测试的几个**重要指标**

![image](https://upload-images.jianshu.io/upload_images/539132-203027b45f8eb569.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/594)

- 重要指标

```
Concurrency Level: 100
//并发请求数
Time taken for tests: 50.872 seconds
//整个测试持续的时间
Complete requests: 1000
//完成的请求数
Failed requests: 0
//失败的请求数

Total transferred: 13701482 bytes
//整个场景中的网络传输量
HTML transferred: 13197000 bytes
//整个场景中的HTML内容传输量

Requests per second: 19.66 [#/sec] (mean)
//吞吐率，大家最关心的指标之一，相当于 LR 中的每秒事务数，后面括号中的 mean 表示这是一个平均值
Time per request: 5087.180 [ms] (mean)
//用户平均请求等待时间，大家最关心的指标之二，相当于 LR 中的平均事务响应时间，后面括号中的 mean 表示这是一个平均值
Time per request: 50.872 [ms] (mean, across all concurrent requests)
//服务器平均请求处理时间，大家最关心的指标之三

Transfer rate: 263.02 [Kbytes/sec] received
//平均每秒网络上的流量，可以帮助排除是否存在网络流量过大导致响应时间延长的问题

```

这段表示网络上消耗的时间的分解

> 网络消耗时间

![image](https://upload-images.jianshu.io/upload_images/539132-04c414bde19255a1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/374)

这段是每个请求处理时间的分布情况，50%的处理时间在4930ms内，66%的处理时间在5008ms内...，重要的是看90%的处理时间。

> 响应情况

![image](https://upload-images.jianshu.io/upload_images/539132-b98dc6d06438b95d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/494)