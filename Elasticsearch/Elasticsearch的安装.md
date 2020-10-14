#### 检查依赖

```
vagrant@homestead:~$ java -version
openjdk version "10.0.2" 2018-07-17
OpenJDK Runtime Environment (build 10.0.2+13-Ubuntu-1ubuntu0.18.04.4)
OpenJDK 64-Bit Server VM (build 10.0.2+13-Ubuntu-1ubuntu0.18.04.4, mixed mode)
vagrant@homestead:~$ which java
/usr/bin/java
```



#### 下载解压

```
vagrant@homestead:~$ wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.3.0-linux-x86_64.tar.gz
vagrant@homestead:~$ tar -xzf elasticsearch-7.3.0-linux-x86_64.tar.gz
vagrant@homestead:~$ cd elasticsearch-7.3.0/ 
// 执行
vagrant@homestead:~$./bin/elasticsearch
```



#### 注意事项：

```
问题产生原因：由于我是windows直接下载，拖到相对应的目录，造成该文件的文件夹拥有者是root

会提示两个原因：

问题提示一：
解决方式是：换非root用户

OpenJDK 64-Bit Server VM warning: Option UseConcMarkSweepGC was deprecated in version 9.0 and will likely be removed in a future release.
[2019-08-07T02:54:39,520][WARN ][o.e.b.ElasticsearchUncaughtExceptionHandler] [homestead] uncaught exception in thread [main]
org.elasticsearch.bootstrap.StartupException: java.lang.RuntimeException: can not run elasticsearch as root
	at org.elasticsearch.bootstrap.Elasticsearch.init(Elasticsearch.java:163) ~[elasticsearch-7.3.0.jar:7.3.0]
	at org.elasticsearch.bootstrap.Elasticsearch.execute(Elasticsearch.java:150) ~[elasticsearch-7.3.0.jar:7.3.0]
	at org.elasticsearch.cli.EnvironmentAwareCommand.execute(EnvironmentAwareCommand.java:86) ~[elasticsearch-7.3.0.jar:7.3.0]
	at org.elasticsearch.cli.Command.mainWithoutErrorHandling(Command.java:124) ~[elasticsearch-cli-7.3.0.jar:7.3.0]
	at org.elasticsearch.cli.Command.main(Command.java:90) ~[elasticsearch-cli-7.3.0.jar:7.3.0]
	at org.elasticsearch.bootstrap.Elasticsearch.main(Elasticsearch.java:115) ~[elasticsearch-7.3.0.jar:7.3.0]
	at org.elasticsearch.bootstrap.Elasticsearch.main(Elasticsearch.java:92) ~[elasticsearch-7.3.0.jar:7.3.0]
Caused by: java.lang.RuntimeException: can not run elasticsearch as root
	at org.elasticsearch.bootstrap.Bootstrap.initializeNatives(Bootstrap.java:105) ~[elasticsearch-7.3.0.jar:7.3.0]
	at org.elasticsearch.bootstrap.Bootstrap.setup(Bootstrap.java:172) ~[elasticsearch-7.3.0.jar:7.3.0]
	at org.elasticsearch.bootstrap.Bootstrap.init(Bootstrap.java:349) ~[elasticsearch-7.3.0.jar:7.3.0]
	at org.elasticsearch.bootstrap.Elasticsearch.init(Elasticsearch.java:159) ~[elasticsearch-7.3.0.jar:7.3.0]
	... 6 more
	
问题提示二：
解决方式是：更换文件夹拥有者：chown -R vagrant：vagrant elasticsearch-7.3.0

Exception in thread "main" java.lang.RuntimeException: starting java failed with [1]
output:
[0.001s][error][logging] Error opening log file 'logs/gc.log': Permission denied
[0.001s][error][logging] Initialization of output 'file=logs/gc.log' using options 'filecount=32,filesize=64m' failed.
error:
OpenJDK 64-Bit Server VM warning: Option UseConcMarkSweepGC was deprecated in version 9.0 and will likely be removed in a future release.
Could not rename log file 'logs/gc.log' to 'logs/gc.log.01' (Permission denied).
Invalid -Xlog option '-Xlog:gc*,gc+age=trace,safepoint:file=logs/gc.log:utctime,pid,tags:filecount=32,filesize=64m', see error log for details.
Error: Could not create the Java Virtual Machine.
Error: A fatal exception has occurred. Program will exit.
	at org.elasticsearch.tools.launchers.JvmErgonomics.flagsFinal(JvmErgonomics.java:111)
	at org.elasticsearch.tools.launchers.JvmErgonomics.finalJvmOptions(JvmErgonomics.java:79)
	at org.elasticsearch.tools.launchers.JvmErgonomics.choose(JvmErgonomics.java:57)
	at org.elasticsearch.tools.launchers.JvmOptionsParser.main(JvmOptionsParser.java:89)
```



#### 修改配置

```
ubuntu@ubuntu:~/elasticsearch-7.3.0/bin$ vim ../config/elasticsearch.yml
//开启外网访问
network.host: 0.0.0.0

修改配置后试运行,报了下面两个错误

ERROR: [2] bootstrap checks failed
[1]: max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
[2]: the default discovery settings are unsuitable for production use; at least one of [discovery.seed_hosts, discovery.seed_providers, cluster.initial_master_nodes] must be configured

第一个错误：
问题翻译过来就是：elasticsearch用户拥有的内存权限太小，至少需要262144；
解决办法：
    切换到root用户执行命令：
    # sudo sysctl -w vm.max_map_count=262144

    查看结果：
    # sysctl -a|grep vm.max_map_count

    显示：
    # vm.max_map_count = 262144

    上述方法修改之后，如果重启虚拟机将失效，所以解决办法：
    在/etc/sysctl.conf文件最后添加一行,即可永久修改
    # vm.max_map_count=262144

第二个错误：
解决办法：
	// 编辑配置文件
	ubuntu@ubuntu:~/elasticsearch-7.3.0$ vim config/elasticsearch.yml
	// 添加以下内容
	# cluster.initial_master_nodes: ["node-1", "node-2"]
	cluster.initial_master_nodes: ["node-1"] 这个的话，这里的node-1是上面一个默认的记得打开就可以了
```





相关链接：

- 官方操作文档：<https://www.elastic.co/guide/en/elasticsearch/reference/current/targz.html>

- 安装笔记：<https://www.twblogs.net/a/5c373591bd9eee35b21d55e9/zh-cn>
- 安装问题合辑：<https://ask.zkbhj.com/?/question/312>