### WebBench安装：
```
yum install ctags（先安装依赖库）

mkdir /usr/local/man （创建一个目录不然会报错）

wget http://home.tiscali.cz/~cz210552/distfiles/webbench-1.5.tar.gz

tar zxvf webbench-1.5.tar.gz

cd webbench-1.5

make

make install

```

### 使用 
```
webbench -c 并发数 -t 运行测试时间 URL

webbench -c 1000 -t 60 http://192.168.80.157/phpinfo.php

```

