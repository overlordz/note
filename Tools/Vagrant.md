#### Windows安装与使用：

1. 下载软件有：Vagrant 、 VirtualBox 、xshell，然后直接在Windows安装，然后重启；
2. Box的创建：
-   vagrant init
-   vagrant box list  查看box
-   vagrant box add devbox  unbuntu.box (线上)
-   vagrant box add laravel/homestead d:/code/homestead.box (本地)
-   配置好Vagrantfile文件
3. 启动vagrant
-   用cmd进入所对应的box文件：	D:/vagrant1
-   输入命令启动：vagrant up
-   登录ssh：vagrant ssh


#### vagrant常用命令

| 命令               | 解析                  |
| ------------------ | --------------------- |
| vagrant box list   | 查看目前已有的box     |
| vagrant box add    | 新增一个box           |
| vagrant box remove | 删除指定box           |
| vagrant init       | 初始化配置vagrantfile |
| vagrant up         | 启动虚拟机            |
| vagrant ssh        | ssh登录虚拟机         |
| vagrant suspend    | 挂起虚拟机            |
| vagrant reload     | 重启虚拟机            |
| vagrant halt       | 关闭虚拟机            |
| vagrant status     | 查看虚拟机状态        |
| vagrant destroy    | 删除虚拟机            |

 

vagrant 修改配置生效

> vagrant reload --provision

