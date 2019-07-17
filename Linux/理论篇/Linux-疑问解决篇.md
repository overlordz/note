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



#### # Linux中shell变量$#,$@,$0,$1,$2的含义解释

```
linux中shell变量$#,$@,$0,$1,$2的含义解释: 
变量说明: 
$$ 
Shell本身的PID（ProcessID） 
$! 
Shell最后运行的后台Process的PID 
$? 
最后运行的命令的结束代码（返回值） 
$- 
使用Set命令设定的Flag一览 
$* 
所有参数列表。如"$*"用「"」括起来的情况、以"$1 $2 … $n"的形式输出所有参数。 
$@ 
所有参数列表。如"$@"用「"」括起来的情况、以"$1" "$2" … "$n" 的形式输出所有参数。 
$# 
添加到Shell的参数个数 
$0 
Shell本身的文件名 
$1～$n 
添加到Shell的各参数值。$1是第1参数、$2是第2参数…

示列：

#!/bin/bash
#
printf "The complete list is %s\n" "$$"
printf "The complete list is %s\n" "$!"
printf "The complete list is %s\n" "$?"
printf "The complete list is %s\n" "$*"
printf "The complete list is %s\n" "$@"
printf "The complete list is %s\n" "$#"
printf "The complete list is %s\n" "$0"
printf "The complete list is %s\n" "$1"
printf "The complete list is %s\n" "$2

#执行脚本
# $ ./params.sh 123456 QQ

#输出结果
The complete list is 24249
The complete list is
The complete list is 0
The complete list is 123456 QQ
The complete list is 123456
The complete list is QQ
The complete list is 2
The complete list is params.sh
The complete list is 123456
The complete list is QQ
```



#### #Linux中/dev/null与2>&1 讲解

首先先来看下几种标识的含义：

- /dev/null 表示空设备文件
- 0 表示stdin标准输入
- 1 表示stdout标准输出
- 2 表示stderr标准错误

shell中可能经常能看到：>/dev/null  2>&1
命令的结果可以通过 %> 的形式来定义输出,其中 %> 代表文件描述符
我们将这个命令组合：“>/dev/null 2>&1”  拆为四部分来分析下:

1：首先 0> 表示stdin标准输入; 1> 表示stdout标准输出; 2> 表示stderr错误输出;
2：符号 >  等价于 1> (系统默认为1,省略了先); 所以">/dev/null"等同于 "1>/dev/null"
3：/dev/null 代表空设备文件
4：& 可以理解为是"等同于"的意思，2>&1，即表示2的输出重定向等同于1
因此，>/dev/null 2>&1 也可以写成“1> /dev/null 2> &1”

那么本文标题的语句执行过程为：
1>/dev/null：首先表示标准输出重定向到空设备文件，也就是不输出任何信息到终端，说白了就是不显示任何信息。
2>&1 ：接着，将标准错误输出重定向到标准输出，因为之前标准输出已经重定向到了空设备文件，所以标准错误输出也重定向到空设备文件。
还看到kafka在启动的脚本里面这么写到：

> nohup bin/kafka-server-start.sh config/server.properties  > /dev/null 2>&1 &

查了一下：
在命令最后加上&符号，表示让这个进程到后台去执行，这样立刻返回到提示符状态，我们可以接着做下面的事。如：command &。
这种“后台”进程在shell一直打开的情况下是没有问题的，如果我们关了shell窗口甚至退出ssh登录或vnc登录，那么进程自动就结束了。所以如果想退出窗口乃至退出登录仍然保持程序运行，再加上nohup，

形如：nohup command 1>output 2>&1 &