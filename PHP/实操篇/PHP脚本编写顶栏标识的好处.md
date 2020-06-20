## php脚本#!/usr/bin/env php写法的好处

最近在写PHP脚本发现有些脚本有这么个写法，长见识了。

脚本语言的第一行，目的就是指出，你想要你的这个文件中的代码用什么可执行程序去运行它。

比如php脚本的第一行可以写成如下几种格式

```shell
#!/usr/bin/php
#!/usr/bin/env php
#!/usr/bin/env -S -P/usr/local/bin:/usr/bin php
#!/usr/bin/env -S-P/usr/local/bin:/usr/bin:${PATH} php
```



> \#!/usr/bin/env php

 这种写法主要是为了让你的程序在不同的系统上都能适用。

不管你的 php 是在 **/usr/bin/php** 还是 **/usr/local/bin/php**
**\#!/usr/bin/env php** 会自动的在你的用户 PATH 变量中所定义的目录中寻找 php 来执行的。

加上-P参数使用来指定一些目录去寻找php这个程序
\#!/usr/bin/env -S -P/usr/local/bin:/usr/bin php的作用就是在/usr/local/bin和/usr/bin目录下寻找php。

为了让程序更加的有可扩展性，可以写成如下代码：

```shell
#!/usr/bin/env -S-P/usr/local/bin:/usr/bin:${PATH} php
#!/usr/bin/env -S-P/usr/local/bin:/usr/bin:${PATH} perl
```


（-S-P之间没有空格）

那么它除了在这两个目录寻找之外，还会在PATH变量中定义的目录中寻找。

\#!/usr/bin/php写成 #!/usr/bin/env php会好些，当然更好的是#!/usr/bin/env -S-P/usr/local/bin:/usr/bin:${PATH} php
一般使用#!/usr/bin/env php这种写法就能兼容大部分服务器了。

同理，python脚本开头写法：

\#!/usr/bin/env python





**文章出自**：https://www.cnblogs.com/zqifa/p/php-shell-1.html