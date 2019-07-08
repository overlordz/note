#### # Centos7.0 没有netstat 命令问题?

> 解决办法：

```
yum install net-tools 
```

#### #执行bash脚本文件提示：/bin/bash^M: bad interpreter: 没有那个文件或目录？

```
原因：window和linux的文件格式的不兼容

解决方法：
// 1-编辑问题文件
vim filename
// 2-查看文件格式，可以看到dos或unix的字样. 如果的确是dos格式的
:set ff? 
// 3-修改文件格式
:set ff=unix
```

