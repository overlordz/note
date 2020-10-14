#### 说明
> pip是一个安装和管理Python包的工具

#### 安装

```
Centos7
# yum install -y python-pip 

Ubuntu
# sudo apt-get install python-pip
```



#### thumbor安装

> Thumbor是基于Python的开源的On-Demand图片处理服务，可以实现图片裁剪crop、缩放resize、翻转flip、滤镜filter，甚至是人脸识别。 

```
方式一
    pip install git+git://github.com/thumbor/thumbor.git
方式二
    git clone git://github.com/thumbor/thumbor.git
    cd thumbor
    python setup.py install
    
    参考链接：http://rensanning.iteye.com/blog/2389847
    
    
使用：http://192.168.1.208:8888/unsafe/1000x200/http://h.hiphotos.baidu.com/image/h%3D300/sign=777ba8a7b0315c605c956defbdb0cbe6/a5c27d1ed21b0ef48c509cecd1c451da80cb3ec3.jpg
```