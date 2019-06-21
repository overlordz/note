# Github+Hexo搭建个人博客

##### # 前言

查看一些技术博客时发现他们的域名都是 ***.github.io **，就怀着好奇的心思百度了解一下，原来github可以创建免费的博客网站，然后只要再利用Hexo那就可以搭建好看的博客了。



##### # 准备工作

```javascript
1. 有一个github账号，没有的话去注册一个；
2. 安装了nodejs、npm，并了解一些相关基础知识；
```



##### # 搭建GitHub博客

1. 创建仓库
建一个名为 你的用户名.github.io 的仓库，比如说，如果你的github用户名是yourname，那么你就新建yourname.github.io的仓库（必须是你的用户名，其它名称无效），之后你的网站访问地址就是 https://yourname.github.io 。

![github201906191432.png](../assets/other/github201906191432.png)

2. 创建一个html文件提交访问看看

   ![github201906191441](../assets/other/github201906191441.png)

   ![github201906191441](../assets/other/github201906191443.png)




##### # 绑定阿里云域名（其他原理相同）

1. 在github项目根目录**创建CNAME文件** （注意没有后缀，且文件名是大写），然后在该文件增加**你的域名**，告诉Github Pages服务器你想指定的域名。该域名不能包含前缀信息，即不能添加http://前缀。 

![github201906191448](../assets/other/github201906191448.png)



2. 阿里云云解析设置如下图

![github201906191436](../assets/other/github201906191436.png)



上面都设置后然后等几分钟，就可以通过域名访问github博客。



记录类型

```
A记录： 将域名指向一个IPv4地址（例如：8.8.8.8）
CNAME：将域名指向另一个域名（例如www.baidu.com）
MX： 将域名指向邮件服务器地址
TXT： 可任意填写，长度限制255，通常做SPF记录（反垃圾邮件）
NS： 域名服务器记录，将子域名指定其他DNS服务器解析
AAAA：将域名指向一个iPv6地址（例如：ff06:0:0:0:0:0:0:c3）
```



主机记录

```
www： 解析后的域名为www.baidu.com
@： 直接解析主域名 baidu.com
*： 泛解析，匹配其他所有域名 *.baidu.com
二级域名： 如：abc.qcloud.com，填写abc
```

