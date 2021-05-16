> 原文地址 [learnku.com](https://learnku.com/articles/54352)

在我们接触的 shell 脚本中，对 `2>&1` 一定不陌生，比如 `ls foo > /dev/null 2>&1`。

本文就来解释下 `2>&1` 究竟做了什么，并且是如何起作用的。

一、I/O 重定向简介
-----------------------

「重定向」是计算机用来把命令的输出从一个地方，输出到另一个地方。举个例子，默认情况下，我们使用 `cat` 指令可以把一个文件的内容打印到终端：

```
$ cat foo.txt

foo
bar
baz

```

但是，我们可以把输出重定向到另外地方。此例中，我们可以把输出重定向到 `output.txt` 中：

```
$ cat foo.txt > output.txt

$ cat output.txt
foo
bar
baz

```

注意，在执行第一行命令 `cat foo.txt > output.txt` 时，我们在屏幕上看不到任何输出。我们把原来应该打印到屏幕的内容，重定向到 `output.txt` 了，所以屏幕上不会有任何输出了。

这里，「本来应该打印到屏幕的内容」，就是**标准输出**，即 **stdout(standard output)**。

除了**标准输出**可以接收程序的输出之外，还有一个地方可以，叫 **标准错误输出**，即 **stderr(standard error)**。stderr 用来接收程序的错误消息。例如，我们 `cat` 了一个不存在的文件：

```
$ cat nop.txt > output.txt
cat: nop.txt: No such file or directory

```

我们看到，虽然我们要求程序把输出重定向到 `output.txt`，但是我们还是在屏幕上看到了错误消息输出。这是因为我们只是重定向了 standard output，而不是 standard error。

二、文件描述符 (fd) 简介
---------------------------

文件描述符（file descriptor）简单来说，就是一个正整数，用来代表一个打开的文件。比如当前我们有 100 各打开的文件，那么就有 100 个文件描述符。

唯一需要补充的是，在 Unix 系统中，「一切皆文件」。

同时我们还应该知道，对于标准输出 (stdout) 和标准错误输出 (stderr)，也有对应的文件描述符。我们使用 1 和 2 来分别表示 stdout 和 stderr 所在的位置。

三、融合上述知识
--------------------

回到我们的第一个示例，我们还可以有另外一种写法

```
# 写法一
$ cat foo.txt > output.txt

# 等价写法二
$ cat foo.txt 1> output.txt

```

这里的 1 就是用来代表 stdout 的文件描述符。语法是 `[FILE_DESCRIPTOR]>`。我们看到把 1 省略的写法 `>` 只是 `1>` 的快捷写法而已。

对于重定向到 stderr 的场景，我们只需要在右边的文件前面加上文件描述符即可

```
$ cat nop.txt 2> error.txt

$ cat error.txt
cat: nop.txt: No such file or directory

```

你看，这样就生效了。这会儿，你大概知道 `2>&1` 是怎样工作的，让我们来总结总结。

我们使用 `&1` 来表示文件描述符 1 (stdout) 的地址。当你使用 `2>&1` 时，其实就是在说：把 stderr 的输出重定向到 stdout 的地方。这样，我们就可以把程序的标准输出和错误输出都输出到同一个地方了。

```
$ cat foo.txt > output.txt 2>&1

$ cat output.txt
foo
bar
baz

$ cat nop.txt > output.txt 2>&1

$ cat output.txt
cat: nop.txt: No such file or directory

```

四、总结
----------------

*   程序可以把输出发送到两个地方：标准输出 (stdout，standard output) 和标准错误输出 (stderr，standard error)。
*   你可以把输出重定向到另一个地方（比如文件）
*   文件描述符 1 和 2 可以分别用来表示 stdout 和 stderr
*   `command > output` 是 `command 1> output` 的缩写
*   可以使用 `&[FILE_DESCRIPTOR]` 来引用文件描述符的值（或者叫指向文件描述符的地址）
*   使用 `2>&1` 来重定向 stderr 的输出至 stdout 的地方（你可以用 `1>&2` 来进行反向操作）
