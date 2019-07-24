这个问题出现服务器有多个 PHP 版本，`php -v`和`phpinfo()`显示两个不同的版本



`phpinfo`查看的 php 版本是 7.2.6，到 bash 去使用`php -v`查看的确是 5.4.2

直接上手先改环境变量，全局永久生效的那种~

```bash
vi /etc/profile
```

在最后加入对应的 PHP 路径

```bash
export PATH=$PATH:/usr/local/php/bin
如果上面不行，换下面试试：
export PATH=/usr/local/php/bin/:$PATH
```

保存，然后让配置生效

```bash
source /etc/profile
```

使用`php -v`查看还是 5.4.2 的版本，环境变量还是指向旧的，没生效

这样，先查一下对应的 php.ini，使用命令，查到有 3 个 php.ini

```bash
$ find / -name php.ini

/usr/local/php/etc/php.ini
/usr/local/php7.0/etc/php.ini
/etc/php.ini
```

然后使用命令查看 PHP 加载的是哪个配置文件，这个获取的是系统内的 php.ini 的位置，不是 Apache 或者 Nginx 加载的 php.ini 文件

```bash
$ php -i | grep php.ini

Configuration File (php.ini) Path => /etc
Loaded Configuration File => /etc/php.ini
```

查找一下系统里的 PHP 相关目录

```php
$ find / -name php
```

可以看到有一个`/usr/bin/php`

这个应该是服务器默认安装的 PHP

ok，使用命令删掉`/usr/bin/php`文件夹

```bash
rm -rf /usr/bin/php
```

别滥用，别敲错，回车

再让配置生效一下

```bash
source /etc/profile
```

这样，在 Bash 里查看的 PHP 版本也就是使用`phpinfo()`查看的版本了