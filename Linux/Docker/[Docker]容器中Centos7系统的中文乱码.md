> 参考文章 ： https://www.cnblogs.com/djlsunshine/p/11282867.html



查看当前语言环境

```
[root@645436bbf1f5 /]# locale
LANG=
LC_CTYPE="POSIX"
LC_NUMERIC="POSIX"
LC_TIME="POSIX"
LC_COLLATE="POSIX"
LC_MONETARY="POSIX"
LC_MESSAGES="POSIX"
LC_PAPER="POSIX"
LC_NAME="POSIX"
LC_ADDRESS="POSIX"
LC_TELEPHONE="POSIX"
LC_MEASUREMENT="POSIX"
LC_IDENTIFICATION="POSIX"
LC_ALL=
[root@645436bbf1f5 /]#
```



不带中文环境

```
[root@645436bbf1f5 ~]# mkdir 杜
[root@645436bbf1f5 ~]# ls
anaconda-ks.cfg  ???
```



添加中文环境编码，安装两个包

```
# yum install kde-l10n-Chinese -y

# yum install glibc-common -y
```



转化语言环境和字符集

```
# localedef -c -f UTF-8 -i zh_CN zh_CN.utf8
```



添加定义到系统环境变量

```
# vi /etc/profile
# 内容底部加下
export LC_ALL=zh_CN.utf8
```



执行生效

```
# source /etc/profile
```



