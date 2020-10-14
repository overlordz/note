### error_reporting() 函数

PHP 有诸多错误级别，使用该函数可以设置在脚本运行时的级别。如果没有设置可选参数 *level*，error_reporting() 仅会返回当前的错误报告级别。

```php
<?php
 // 关闭错误报告
 error_reporting(0);

 // 报告 runtime 错误
 error_reporting(E_ERROR | E_WARNING | E_PARSE);

 // 报告所有错误
 error_reporting(E_ALL);

 // 等同 error_reporting(E_ALL);
 ini_set("error_reporting", E_ALL);

 // 报告 E_NOTICE 之外的所有错误
 error_reporting(E_ALL & ~E_NOTICE); 
 // 等同上面一样效果
 error_reporting(E_ALL ^E_NOTICE); 
?> 

错误报告级别是位字段的叠加，推荐使用 E_ALL | E_STRICT 
	1 E_ERROR 致命的运行时错误 
	2 E_WARNING 运行时警告(非致命性错误) 
	4 E_PARSE 编译时解析错误 
	8 E_NOTICE 运行时提醒(经常是bug，也可能是有意的) 
	16 E_CORE_ERROR PHP启动时初始化过程中的致命错误 
	32 E_CORE_WARNING PHP启动时初始化过程中的警告(非致命性错) 
	64 E_COMPILE_ERROR 编译时致命性错 
	128 E_COMPILE_WARNING 编译时警告(非致命性错) 
	256 E_USER_ERROR 用户自定义的致命错误 
	512 E_USER_WARNING 用户自定义的警告(非致命性错误) 
	1024 E_USER_NOTICE 用户自定义的提醒(经常是bug，也可能是有意的) 
	2048 E_STRICT 编码标准化警告(建议如何修改以向前兼容) 
	4096 E_RECOVERABLE_ERROR 接近致命的运行时错误，若未被捕获则视同E_ERROR 
	6143 E_ALL 除E_STRICT外的所有错误(PHP6中为8191,即包含所有)
```



**特别注意：** **ini_set()**

```
// ini_set()错误警告级别最大，比error_reporting 要大，如果ini_set的值设为OFF，无论error_reporting 的值设置了E_ALL都没用，不会显示任何错误信息；
ini_set('display_errors', 1);  // 开启
ini_set('display_errors', 0);  // 关闭
```