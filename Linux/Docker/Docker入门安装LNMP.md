地址：https://hub.docker.com/r/2233466866/lnmp

拉取镜像

```
docker pull 2233466866/lnmp
```

第一次安装：

docker run -dit \
-p 80:80 \
-p 443:443 \
-p 3306:3306 \
-p 9000:9000 \
-v /mnt/e/Code/www:/www \
-v /mnt/e/Code/mysql:/data/mysql \
--privileged=true \
--name=lnmp \
2233466866/lnmp:newest

# 容器名称与上一步保持一致

进入容器：

> docker exec -it lnmp /bin/bash

如果发现有些没有bash就直接  /bin/sh



第二次直接启动docker

docker start lnmp


curl -s https://laravel.build/laravel-koala | bash

composer create-project laravel/laravel laravel-koala


Bug 处理：

```
安装 2233466866/lnmp:newest ，mysql 启动失败如何解决

解决了，分享一下过程：
1、清除mysql 下的所有文件
2、/var/log/mysqld.log 赋予权限:chmod 777 mysqld.log
```


显示临时密码：

初始密码(Default password)

cat /var/log/mysqld.log|grep 'A temporary password'

 或

password=`cat /var/log/mysqld.log|grep 'A temporary password'`;password=${password:91};echo $password

显示临时密码：

> Ch*v2)z3spfB

修改密码：

alter user 'root'@'localhost' identified by 'koala123';

创建远程用户：

create user koala identified by 'koala123';

grant all privileges on *.* to 'koala'@'%' identified by 'koala123' with grant option;

flush privileges;





#### Windows 10如何给已经启动的Docker容器添加或者修改端口映射（通过修改配置文件实现）

> 参考文档链接：https://www.wuleba.com/555.html

Linux 下容器配置文件在这个路径：/var/lib/docker/containers/[hash_of_the_container]

所以只要找到 Windows 系统下类似的路径就可以了。

后面发现在 Windows 10 系统中文件在这个路径：\\wsl$\docker-desktop-data\version-pack-data\community\docker\containers\[hash_of_the_container]

hash_of_the_container 可以通过 docker inspect 容器名字 来查看，一般都是 Docker 容器ID 开头。

> docker inspect 79ae4d783f8a | grep Id



这些文件不是挂在本地目录，而是挂在 Network 目录下。反正你们打开我的电脑，然后在地址栏输入：\\wsl$\docker-desktop-data\version-pack-data\community\docker\containers\   就可以打开了。

关闭你们的 Container 容器，然后修改 hostconfig.json 配置文件：

然后直接修改这个目录下的 hostconfig.json 就可以修改和添加指定容器的端口映射了，修改 hostconfig.json 配置文件，记得备份哦，文件内容是 json 格式，格式化后：

```
    "PortBindings": {
        "3306/tcp": [
            {
                "HostIp": "",
                "HostPort": "3306"
            }
        ],
        "443/tcp": [
            {
                "HostIp": "",
                "HostPort": "443"
            }
        ],
        "80/tcp": [
            {
                "HostIp": "",
                "HostPort": "80"
            }
        ],
        "9000/tcp": [
            {
                "HostIp": "",
                "HostPort": "9000"
            }
        ],
        "9501/tcp": [  //新增
            {
                "HostIp": "",
                "HostPort": "9501"
            }
        ]
    },
```



**修改 config.v2.son 配置文件：**

```
        "Ports": {
            "3306/tcp": [
                {
                    "HostIp": "0.0.0.0",
                    "HostPort": "3306"
                }
            ],
            "443/tcp": [
                {
                    "HostIp": "0.0.0.0",
                    "HostPort": "443"
                }
            ],
            "80/tcp": [
                {
                    "HostIp": "0.0.0.0",
                    "HostPort": "80"
                }
            ],
            "9000/tcp": [
                {
                    "HostIp": "0.0.0.0",
                    "HostPort": "9000"
                }
            ],
            "9501/tcp": [ // 新增
                {
                    "HostIp": "0.0.0.0",
                    "HostPort": "9501"
                }
            ]
        },
```



修改完毕之后，要重启整个Docker服务，不是直接去Docker Desktop重启 Container 哦（如果你不小心启动了 Container  ，上面的配置会被还原，请你再改一次，然后这次就不要忘记 Restart Docker 服务了），在系统右下角使用 `Docker Desktop` 中restart 服务  即可正常使用~~
