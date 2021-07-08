**1、查看磁盘空间情况：**

既然是定时备份，就要选择一个空间充足的磁盘空间，避免出现因空间不足导致备份失败，数据丢失的恶果！ 

存储到当前磁盘这是最简单，却是最不推荐的；服务器有多块硬盘，最好是把备份存放到另一块硬盘上；有条件就选择更好更安全的存储介质；

```
# df -hFilesystem
```

**2、创建备份目录：**

上面我们使用命令看出 / home 下空间比较充足，所以可以考虑在 / home 保存备份文件；

```
cd /homemkdir 
mkdir backupcd backup
```

**3、创建备份 Shell 脚本:**

注意把以下命令中的 DatabaseName 换为实际的数据库名称； 
当然，你也可以使用其实的命名规则！

```
vi bkDatabaseName.sh
```

输入 / 粘贴以下内容：

```
#!/bin/bashmysqldump -uusername -ppassword DatabaseName > /home/backup/DatabaseName_$(date +%Y%m%d_%H%M%S).sql
```

对备份进行压缩：

```
#!/bin/bashmysqldump -uusername -ppassword DatabaseName | gzip > /home/backup/DatabaseName_$(date +%Y%m%d_%H%M%S).sql.gz
```

**注意：** 
把 username 替换为实际的用户名； 
把 password 替换为实际的密码； 
把 DatabaseName 替换为实际的数据库名；

**4、添加可执行权限：**

```
chmod u+x bkDatabaseName.sh
```

添加可执行权限之后先执行一下，看看脚本有没有错误，能不能正常使用；

```
./bkDatabaseName.sh
```



 **5、添加计划任务**



**检测或安装 crontab**

确认 crontab 是否安装： 
执行 crontab 命令如果报 command not found，就表明没有安装

如时没有安装 crontab，需要先安装它，具体步骤请参考： 
CentOS 下使用 yum 命令安装计划任务程序 crontab 
使用 rpm 命令从 CentOS 系统盘安装计划任务程序 crontab

**添加计划任务**

执行命令：

```
crontab -e
```

这时就像使用 vi 编辑器一样，可以对计划任务进行编辑。 
输入以下内容并保存：

```
*/1 * * * * /home/backup/bkDatabaseName.sh
```

具体是什么意思呢？ 
意思是每一分钟执行一次 shell 脚本

“/home/backup/bkDatabaseName.sh”。

**6、测试任务是否执行**

很简单，我们就执行几次 “ls” 命令，看看一分钟过后文件有没有被创建就可以了！

如果任务执行失败了，可以通过以下命令查看任务日志：

```
# tail -f /var/log/cron
```
