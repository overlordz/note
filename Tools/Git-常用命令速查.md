Git 常用命令速查表



master : 默认开发分支

origin : 默认远程版本库

Head  : 默认开发分支

Head^ : Head的父提交



#### 创建版本库

$ git clone <url>   #克隆远程版本库

$ git init	#初始化本地版本库



#### 修改和提交

$ git status	#查看状态

$ git diff	#查看变更内容

$ git add .	#跟踪所有改动过的文件

$ git add <file>	#跟踪指定的文件

$ git mv <old> <new>	#文件改名

$ git rm <file>	#删除文件

$ git rm --cache <file>	#停止跟踪文件但不删除

$ git commit -m "commit message"	#提交所有更新过的文件

$ git commit --amend	#修改最后一次提交



#### 查看提交历史

$ git log	# 查看提交历史

$ git log -p <file>	#查看指定文件的提交历史

$ git blame <file>	#以列表方式查看指定文件的提交历史



#### 撤销

$ git reset --hard HEAD	#撤销工作目录中所有未提交文件的修改内容

$ git checkout HEAD <file>	#撤销指定的未提交文件的修改内容

$ git revert <commit>	#撤销指定的提交



#### 分支与标签

$ git branch	#显示所有本地分支

$ git checkout <branch/tag>	#切换到指定分支或标签

$ git branch <new-branch>	#创建新分支

$ git branch -d <branch>	#删除本地分支

$ git tag	#列出所有本地标签

$ git tag <tagname>	#基于最新提交创建标签

$ git tag -d <tagname>	#删除标签



#### 合并与衍和

$ git merge <branch>	#合并指定分支到当前分支

$ git rebase <branch>	#衍合指定分支到当前分支



#### 远程操作

$ git remote -v	#查看远程版本库信息

$ git remote show <remote>	#查看指定远程版本库信息

$ git remote add <remote> <url>	#添加远程版本库

$ git fetch <remote>	#从远程库获取代码

$ git pull <remote> <branch>	#从远程库获取代码

$ git push <remote><branch>	#上传代码及快速合并

$ git push <remote> : <branch/tag-name>	#删除远程分支或标签

$ git push --tags	#上传所有标签



#### 运维操作

##### 1、对比打包文件

> 当前分支对比某个提交的code进行对比，并生成对应的压缩包（7z、zip）

$ git diff HEAD d5ae2d412e3c0dc5e7a3c5d610b0e18a8270a23f --name-only | xargs tar -rf gitee.7z

##### 2、直接打包

1. 基本用法

```bash
git archive --format tar.gz --output "./output.tar.gz" master
```

说明：

- 将master分支打包为output.tar.gz，支持打包格式有 **tar、tgz、tar.gz、zip**

- --format指明打包格式，若不指明此项，则根据--output中的文件名推断文件格式。所以你也可以将上述命令简化为:

```bash
git archive --output "./output.tar.gz" master
```

2. 打包不同的分支或commit

如果想打包不同分支，只要使用不同分支名即可。比如我有一个分支名为“testbr”，可以用如下命令将其打包。

```bash
git archive --format tar.gz --output "./output.tar.gz" testbr
```

如果要打包某个commit，只要先使用git log找到相应的commit id, 再使用该commit id打包即可。比如：

```bash
git archive --format tar.gz --output "./output.tar.gz" 5ca16ac0d603603
```

3. 打包某个目录

如果想打包master下的mydir mydir2目录，可以用下面命令

```bash
git archive --format tar.gz --output "./output.tar.gz" master mydir mydir2  
```