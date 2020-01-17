# Vagrant

**vagrant是什么**

>  Vagrant是一款用来构建虚拟开发环境的外挂工具，可以简化虚拟机配置和管理。它底层支持VirtualBox、VMware、AWS等，非常适合使用php/python/ruby/java语言开发web应用，“代码在我机子上运行没有问题”这种说辞将成为历史。



**box是什么**

box是Vagrant中的一个概念，其包含了操作系统和项目运行的依赖环境，比如一个Ruby and Rails项目封装好的box，可能是Ubuntu系统＋git+Ruby/Bundle+Rails+Nodejs+MySQL。



## Windows安装与使用：

**安装说明**

```
$ vagrant box add {title} {url}
$ vagrant init {title}
$ vagrant up
```

`vagrant box add`是添加box的命令，｛title｝是以后创建虚拟机的别名，｛url｝是下载到本地box的路径，也可以是服务器端的URL。



**将box镜像添加到本地仓库，有三种方式:**

1. 使用http绝对地址

```
vagrant box add homestead https://vagrantcloud.com/laravel/boxes/homestead/versions/7.1.0/providers/virtualbox.box
```

2. 使用本地文件（从协议来说，和上面一样，相当于file:///协议的地址)

```
vagrant box add laravel/homestead d:/code/homestead.box 
```

3. 使用仓库名称

```
vagrant box add homestead laravel/homestead
```

这种方式，vagrant会自动在中央仓库查找镜像，并下载到本地镜像库中。

> vagrant box add laravel/homestead

省略本地镜像名称，则直接用中央仓库中的镜像名作为本地镜像名，这样做的好处是可以跟仓库中的镜像对应。

4. 配置好Vagrantfile文件

   

**启动系统**

1. 进入Vagrantfile文件所对应的目录，启动命令 `vagrant up`

2. 登录虚拟机，命令：`vagrant ssh`

3. 使用第三方工具，如secureCRT）或系统ssh命令进行登录，IP为Vagrantfile中指定的hostonly地址，用户名与密码默认都是vagrant

   在宿主机的命令行，ssh到本地端口的方式登录。观察客户机的22端口映射到宿主机的是多少，一般是2200或者2222，用户名与密码默认都是vagrant

   >  ssh -p 2222 127.0.0.1



## 打包分发

当你配置好开发环境后，退出并关闭虚拟机。在终端里，对虚拟机打包

```
# vagrant halt         // 在开发环境的目录，关闭centos7

# vagrant status       // 确认centos7关机

# vagrant package centos7 --output centos7.box  --vagrantfile Vagrantfile

// --output NAME:(可选)设置通过NAME来指定输出的文件名；--vagrantfile FILE:(可选)将Vagrantfile直接封进box中
```



注：如果网络模式中使用private_network的话，在打包之前需要清除一下private_network的设置，避免不必要的错误：`rm -f /etc/udev/rule.d/70-persistent-net.rules`

打包完成后会在当前目录生成一个centos7.box的文件，将这个文件分发给其他用户（开发者）。

其他用户只要添加这个 box ，并用其初始化自己的开发目录，就能得到一个一模一样的开发环境了。

```
//添加package.box，镜像命名为centos7
# vagrant box add centos7 centos7.box       

// 初始化centos7镜像
$ vagrant init centos7      

$ vagrant up
```



## Vagrant常用命令

| 命令                           | 解析                                           |
| ------------------------------ | ---------------------------------------------- |
| vagrant box list               | 查看目前已有的box                              |
| vagrant box add                | 新增一个box                                    |
| vagrant box remove             | 删除指定box                                    |
| vagarnt destroy                | 彻底移除本地环境                               |
| vagrant init                   | 初始化配置vagrantfile                          |
| **vagrant up**                 | 启动虚拟机                                     |
| vagrant ssh                    | ssh登录虚拟机                                  |
| vagrant suspend                | 挂起虚拟机                                     |
| **vagrant reload**             | 重启虚拟机                                     |
| **vagrant reload --provision** | 重启并修改配置生效                             |
| **vagrant halt**               | 关闭虚拟机                                     |
| vagrant status                 | 查看虚拟机状态                                 |
| vagrant package                | 打包命令，可以把当前的运行的虚拟机环境进行打包 |



## 问题解决


> Vagrant package 打包 Homestead box 出现 “ default: VM not created. Moving on... ”

这是因为底层的虚拟机的名称跟vagrant的名称不一致，

```
$vboxmanage list vms
"ubuntu-xenial-16.04-cloudimg" {02ab228e-7803-43cf-958b-2ab6bc8f69f5}
$vagrant package --base ubuntu-xenial-16.04-cloudimg --output ./Ubuntu.box
```

或直接打开 VirtualBox 查看虚拟机的名称，然后直接导出来

```
$ vagrant package --base homestead-7 --output homestead.box
```






本文内容由以下文章进行二次修改整理：[点击查看](https://blog.csdn.net/yjk13703623757/article/details/70040797)

查看Vagrantfile配置详解，[点击查看](<https://blog.csdn.net/yjk13703623757/article/details/70768981>)

vboxmanage详解：[点击查看](https://segmentfault.com/a/1190000006729204)