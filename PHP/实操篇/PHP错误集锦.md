#### 运行脚本超时

**原因：** php运行时间默认是30秒，如果超出时间就会报错

**解决：**在脚本运行开始添加  `set_time_limit(0)`



#### Fatal error: Allowed memory size of 134217728 bytes exhausted (tried to allocate 4096 bytes) in xxxxxx/xxxx.php on line 999

**原因：**php 默认内存限制是 128M，情况多产生递归函数

**解决：** 

1、需要修改 `php.ini` 文件，查找到 **memory_limit = 128M **这一行，将 128M 改大点，然后重启php-fpm

2、查看某些大的变量 赋值 `null` ，为什么不用 `unset`，因为 `unset` 有时不是真正销毁变量，只是取消变量指向内存的指针



#### Fatal error: Uncaught Error: Maximum function nesting level of ‘256’ reached, aborting! in  /xxxx/xxx.php

**原因：** 这个报错来源于 `xdebug` 的配置，php 没有这个限制

**解决：** 在 `php.ini`  修改  `xdebug.max_nesting_level=1000`