### Siege安装：

> 下载地址：http://download.joedog.org/siege/

```
$ wget http://download.joedog.org/siege/siege-latest.tar.gz
$ tar xvf siege-latest.tar.gz
得到的具体版本就是siege-4.0.9
```

编译安装

```
$ cd siege-4.0.9
编译的时候，我只制定了安装目录：/usr/local/siege/
$ ./configure --prefix=/usr/local/siege/
$ make && make install
```

安装完成后，查看一下安装目录下具体都有哪些目录：

```
$ ll /usr/local/siege/
```

结果如下

```
total 0
drwxr-xr-x 2 root root 74 May 26 14:37 bin
drwxr-xr-x 2 root root 35 May 26 14:37 etc
drwxr-xr-x 3 root root 16 May 26 14:37 share
```

然后使用 /usr/local/siege/bin/siege -help 来查看是否真的安装成功了：

```
$ siege/bin/siege –help
```

如果看到输出信息，则说明安装成功了

### 使用 

siege的具体使用方法很简单，通常使用时用的比较多的就是并发用户数和运行时间

```
$ siege/bin/siege –c 10 –t 60s http://host/xxx
```

-c 并发用户数

-t 运行时间

