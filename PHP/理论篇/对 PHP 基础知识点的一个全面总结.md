> 原文地址 [mp.weixin.qq.com](https://mp.weixin.qq.com/s/Q2l1yFqw-XL2tbBMWrezRA)

PHP 引用变量
--------

*   什么是引用变量？如何定义引用变量？

> 引用意味着用不同的名字访问同一个内容  
> 定义引用变量：使用`&`

*   引用变量的工作原理

> 普通变量的工作原理

```
$a = range(0,10000);
var_dump(memory_get_usage()); //int(742072)
$b = $a;
var_dump(memory_get_usage()); //int(742096)
$a = range(0,30000);
var_dump(memory_get_usage()); //int(1532648)

```

![](https://mmbiz.qpic.cn/mmbiz_png/QibLP1rpwH8veiahqia8SDXdhIYHUeGu99veVeaEwgc0ymDhtAe5y4UPgoicUibXNpARZyD9UrRPAvuNOkPJaFF6jicQ/640?wx_fmt=png)

> 引用变量的工作原理

```
$a = range(0,10000);
var_dump(memory_get_usage()); //int(742072)
$b = &$a;
var_dump(memory_get_usage()); //int(742096)
$a = range(0,30000);
var_dump(memory_get_usage()); //int(1135312)

```

![](https://mmbiz.qpic.cn/mmbiz_png/QibLP1rpwH8veiahqia8SDXdhIYHUeGu99vEkBBRad1CVGD8cFibzFC1xS2ibBia5lvjICJ7Un7SuxMmP318LLdLfQ4Q/640?wx_fmt=png)

```
注意：
1、引用变量一旦定义，此变量永远是引用变量，不可能再变回普通变量
2、引用变量可以修改引用的值，例如：
$a = 'a';
$c = 'c';
$b = &$a;
$b = &$c; //修改引用

```

常量及数据类型
-------

*   字符串的定义方式及各自区别

> `单引号、双引号、heredoc、newdoc`

```
单引号：不能解析变量，只能解析单引号(\')和反斜线(\\)转义字符，比双引号效率高
双引号：解析变量(可使用{}括起来)，能解析所有转义字符
heredoc：功能类似双引号（用于大字符串）
newdoc: 功能类似单引号

```

```
//heredoc定义
$str = <<<EOT
xxxxxxxxxxxx
EOT;

//newdoc定义
$str = <<<'EOT'
xxxxxxxxxxxxxxxx
EOT;

```

*   数据类型

> 浮点数：`不能用于运算/比较运算，因为浮点数不是精确的`  
> FALSE 七种情况：`0、0.0、'0'、''、NULL、false、array()`  
> 超全局数组（九种）：

```
$GLOBALS、$_GET、$_POST、$_SERVER、$_SESSION、$_COOKIE、$_FILES、$_ENV、$_REQUEST

//$GLOBALS包含其他8种
//$_REQUEST包含$_GET、$_POST和$_COOKIE

$_SERVER['REQUESR_URI']与$_SERVER['PATH_INFO']区别

http://localhost/aaa/index.php/foo/bar.html?p=222&q=333
$_SERVER['QUERY_STRING'] = "p=222&q=333";
$_SERVER['REQUEST_URI']  = "/aaa/index.php/foo/bar.html?p=222&q=333";
$_SERVER['SCRIPT_NAME']  = "/aaa/index.php";
$_SERVER['PHP_SELF']     = "/aaa/index.php";
$_SERVER['PATH_INFO'] = ‘/foo/bar.html’

```

*   常量

> 2 种定义方式：define() 与 const

```
define()函数
const是语言结构
区别
define()不可以定义类常量

```

> 预定义常量

```
__FILE__、__LINE__

```

运算符
---

*   PHP 错误运算符`@`

> 当将其放置在一个 PHP 表达式之前，该表达式可能产生的`任何错误信息`都将`被忽略掉`

*   运算符的优先级

运算符优先级

> 递增 / 递减 > ! > 算术运算符 > 大小比较 > (不) 相等比较 > 引用 > ^ > | > 逻辑与 && > 逻辑或 || > 三目 > 赋值 > and > xor > or

```
<?php
    if($a = 3 > 0 && $b = 3 > 0){
        echo 'true';
    }else{
        echo 'false';
    }
?>

// 相当于
if($a = ((3 > 0) && ($b = 3 > 0)))

```

*   比较运算符

> == 与 === 区别

```
等值判断（FALSE七种情况）

```

*   逻辑运算符

> 短路作用：逻辑与 && > 逻辑或 || > 三目 > 赋值 > and > xor > or

*   递增 / 递减运算符

```
1、递增/递减不影响布尔值
2、递减NULL值没有效果
3、递增NULL值为1
4、递增/递减位置不同效果不同

```

流程控制
----

*   `PHP遍历数组的三种方式及各自区别`

> 1.for 循环（while/do...while）  
> 2.foreach 循环  
> 3.while、each()、list() 组合

```
区别：
for循环：只能遍历索引数组
foreach循环：能遍历关联数组和索引数组
while、each()、list()组合：能遍历关联数组和索引数组

foreach循环遍历数组前，会对数组进行reset()操作
while、each()、list()组合则不会进行reset()操作

```

*   `PHP分支结构`

> `if...elseif...` 原则：可能性大的放在前面  
> `switch...case...` 与 if 区别： _判断参数的数据类型只能是整形、浮点、字符串_

```
switch...case...中，如果使用continue与break等价
如果switch...case...外层还有for,那么使用continue2才可以跳出循环
一般switch要比if性能高

```

自定义函数及内部函数
----------

*   变量作用域

> 大部分变量只有一个单独的范围，这个单独范围跨度包含了 include 与 requires 的文件

```
如果在非类中引入文件，该文件内部可以使用全局变量和函数
如果在类中引入文件，该文件内部可以使用函数、类的方法、类的属性

```

*   函数 / 方法传参

> 传参分 2 种：  
> 1、值传递  
> 2、引用传递（函数修改该值，传递的参数必须是`变量`）

```
//引用传递函数定义
$a = 1;
function fn(&$a){
    return ++$a;
}
echo fn($a); //2

```

*   函数返回值

> 可以返回任意数据类型的值  
> 省略 return 默认返回 NULL, 如果要返回多个值请使用数组

```
函数2种方式返回
1、普通返回
2、引用返回
function &fn(){
    $a = 5;
    return $a;
}
$b = &fn();

```

> 让`函数内部修改外部变量的值`，使用`引用传参`  
> 让`外部变量修改函数内部变量的值`，使用`引用返回`

*   引入外部文件

> 引入的代码将继承引入位置的作用域，即_使用 include 或 require 位置可以使用的变量_  
> include 与 require 区别

*   系统内置函数

> `时间日期函数`

```
date()、strtotime()、mktime()、time()、microtime()、date_default_timezone_set()

```

> `IP处理函数`

```
ip2long()、long2ip()

```

> `打印处理函数`

```
print()、printf()、print_r()、echo、sprintf()、var_dump()、var_export()

```

> `序列化及反序列化函数`

```
serialize()、unserialize()

```

> `字符串处理函数`

```
trim()、strrev()

```

> `数组处理函数`

```
array_rande()、array_chunk()、array_push()、array_pop()、array_merge()

```

正则表达式
-----

*   正则表达式作用（四种）

> _`分割、查找、匹配、替换`_ 作用对象：`字符串、字符串、字符串`（重要的事情记 3 遍）

*   正则表达式的组成部分

```
分隔符：正斜线（/）、hash符（#）、取反符号（~）
通用原子：\d（匹配数字）、\D、\w（匹配数字、字母、下划线）、\W、\s（匹配空格）、\S
元字符：
    量词 ？、+、*、{n}、{n,}、{n,m}
    通配符 .
    范围匹配 []、[-]、[^]
模式修正符：i、m、u、U

```

*   向后引用

> 题目：把字符串 '<b>abc</b>' 替换为'abc' 如何使用正则做到？

```
$str = '<b>abc</b>';
$pattern = '/<b>(.*)<\/b>/';
preg_replace($pattern, '\\1', $str);

```

*   贪婪模式

> 题目：把字符串 '<b>abc</b><b>def</b>' 替换为'abcdef' 如何使用正则做到？

```
$str = '<b>abc</b><b>def</b>';
$pattern = '/<b>(.*?)<\/b>/';
$pattern = '/<b>(.*)<\/b>/U';
preg_replace($pattern, '\\1', $str);

```

> `注意：默认是贪婪模式`

*   正则表达式 PHP 函数

> preg_match()、preg_match_all()、preg_replace()、preg_split()

*   中文匹配问题

```
中文分utf-8与gbk两种编码，他们正则表达式不同
utf-8: 0x4e00 - 0x9fa5 
模式修正符需要使用u

```

文件及目录处理
-------

*   打开文件函数：`fopen()`

> 用于打开一个文件，打开时需要指定一个打开模式  
> `打开模式：r/r+、w/w+、x/x+、a/a+、b、t`  
> r+ 追加写 w 覆盖写

*   读取文件函数：`fread()/fgets()/fgetc()`

> `fgets()` 读取一行  
> `fgetc()` 读取字符

*   写入文件函数：`fwrite()/fputs()`

*   关闭文件函数：`fclose()`

*   不需要 fopen() 都能操作文件

> `file_put_contents()`  
> `file_get_contents()`

*   访问远程文件（不推荐开启）

> 开启 allow_url_fopen() ,`http协议只能使用只读，ftp协议可以只能只读或只写`

*   目录操作函数

> 目录名称：`basename()`、`dirname()`、`pathinfo()`  
> 目录读取：`opendir()`、`readdir()`、`closedir()`、`rewinddir()`  
> 目录删除：`rmdir()`  
> 目录创建：`mkdir()`

*   目录遍历

```
//遍历

function loopDir($dir){

    if(is_dir($dir)){
        if($handle = opendir($dir)){
            while (false !== ($file = readdir($handle))) {
                if ($file != "." && $file != "..") {
                    echo $file.'\n';
                    if(filetype($dir.DIRECTORY_SEPARATOR.$file === 'dir')){
                        loopDir($dir.DIRECTORY_SEPARATOR.$file);
                    }
                }
            }
        }
        
    }else{
        echo basename($file).'\n';
    }
}

```

会话控制
----

*   会话控制的三种实现

```
1、使用URL带参数的方式（不安全，不推荐）
2、cookie
3、session

```

*   如果禁用 cookie 后，该如何实现会话控制？

> `只能通过URL把SessionID传过去`

```
<a href="next.php?<?php echo session_name().'='.session_id(); ?>">下一页</a>
简化：
<a href="next.php?<?php echo SID; ?>">下一页</a>

```

面向对象
----

*   访问控制符

> public、protected、private

网络协议
----

*   GET 与 POST 区别

```
1、GET可以收藏成浏览器书签，而POST不行
2、GET可以保存在浏览器历史记录里，而POST不行
3、GET安全性相比POST差
4、GET请求数据编码：application/x-www-form-urlencoded，而POST则除了application/x-www-form-urlencoded编码，还可以是multipart/form-data或者application/json或者text/xml
5、GET请求参数是有长度限制的，url最多2048个字节，而POST没有限制

```

四种数据编码

开发环境及 PHP 配置
------------

*   版本控制软件

![](https://mmbiz.qpic.cn/mmbiz_png/QibLP1rpwH8veiahqia8SDXdhIYHUeGu99vgia6a3icCVtX4YEl0DGNynxeCeIyiaJCazWJOfpTcoCiaAYm0LGE0t8eCA/640?wx_fmt=png)

*   PHP 运行原理

> `主要了解 CGI、FastCGI、PHP-FPM`

```
CGI：是一种通讯协议（已经过时，性能低下）
FastCGI：也是一种通讯协议，是CGI的升级版（现在推荐使用）
PHP-FPM：是FastCGI进程管理器
php-cgi.exe：是PHP脚本解析器，不是FastCGI进程管理器

```

> `PHP 有5种PHP运行模式`：链接

```
1、以CGI模式运行PHP

LoadModule cgi_module modules/mod_cgi.so //要加载apache自带模块

<Files ~ "\.php$">
    Options FollowSymLinks ExecCGI
    AddHandler cgi-script .php
    FcgidWrapper "D:/BtSoft/WebSoft/php/7.1/php-cgi.exe" .php
</Files>

//如果同时打开多个则会有很多php-cgi.exe,并且在执行完成之后消失：

```

![](https://mmbiz.qpic.cn/mmbiz_png/QibLP1rpwH8veiahqia8SDXdhIYHUeGu99vguyTiaj11sibZ2r8Yb3siaxGGvQXVNh0XYoxBuFqLXzwicfa4grGo66wiaw/640?wx_fmt=png)

```
2、以FastCGI模式运行PHP

FastCGI模式根据进程管理器的不同可以分为：Apache内置进程管理器，PHP-FPM进程管理器

Apache内置进程管理器:
LoadModule fcgid_module modules/mod_fcgid.so //要加载apache模块,该模块要单独下载
<IfModule fastcgi_module>
   FastCgiServer /home/weiyanyan/local/apache/cgi-bin/php-cgi -processes 20
   AddType application/x-httpd-php .php
   AddHandler php-fastcgi .php
   Action php-fastcgi /cgi-bin/php-cgi
</IfModule>

PHP-FPM进程管理器:
LoadModule fastcgi_module modules/mod_fcgid.so
<IfModule fastcgi_module>
    FastCgiExternalServer /home/weiyanyan/local/apache/cgi-bin/php-cgi -host 127.0.0.1:9000
    AddType application/x-httpd-php .php
    AddHandler php-fastcgi .php
    Action php-fastcgi /cgi-bin/php-cgi
</IfModule>

```

![](https://mmbiz.qpic.cn/mmbiz_png/QibLP1rpwH8veiahqia8SDXdhIYHUeGu99vkPAwbrO18dBfRl6riblgqOSwvnias62hWcXGdcr75YkyLkvBM4q8OsWQ/640?wx_fmt=png)

> php54 是之前是一种关系，php54 之后另一种关系。  
> php54 之前，php-fpm(第三方编译) 是管理器，php-cgi 是解释器  
> php54 之后，php-fpm(官方自带)，master 与 pool 模式。php-fpm 和 php-cgi 没有关系了。php-fpm 又是解释器，又是管理器

```
3、以Apache模块模式运行PHP

LoadModule php5_module "C:/php5/php5apache2_2.dll" 
AddType application/x-httpd-php .php

```

*   PHP 常见配置

PHP 数组排序
--------

![](https://mmbiz.qpic.cn/mmbiz_png/QibLP1rpwH8veiahqia8SDXdhIYHUeGu99vQ8icoV1fibEe8b3ZGAlEOUtIhTeW1amVxMsEAiaxMlUzf98bGlhIibZqoA/640?wx_fmt=png)
