## hyperf 安装使用

docker 镜像获取

> docker pull hyperf/hyperf

docker 安装

> docker run -v /mnt/e/Code/webCode/hyperf-skeleton:/hyperf-skeleton -p 9501:9501 -it --entrypoint /bin/sh hyperf/hyperf

 启动 Hyperf

> php bin/hyperf.php start