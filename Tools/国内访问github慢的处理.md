##### 中国地区访问 GitHub 为什么很慢？

> GitHub 的[CDN](https://boke112.com/bkwd/6170.html)域名遭到[DNS](https://boke112.com/bkwd/5835.html)污染



##### 如何解决 DNS 污染？

> 通过修改 Hosts 解决污染问题。



##### 如何加速访问GitHub？

1、打开[IPAddress.com](https://boke112.com/goto/aHR0cHM6Ly93d3cuaXBhZGRyZXNzLmNvbS8=)网站，查询下面 3 个网址对应的 IP 地址

- github.com，192.30.253.112 192.30.253.113
- assets-cdn.github.com，101.184.133
- global.ssl.fastly.net，151.101.185.194

2、修改本地电脑系统 `hosts 文件`（PS：路径一般都是 C:\Windows\System32\drivers\etc），直接在最后加入以下代码

```
192.30.253.112 github.com
192.30.253.113 github.com
151.101.184.133 assets-cdn.github.com
151.101.185.194 github.global.ssl.fastly.net
```

3、用“WIN +Ｒ”快捷键打开运行窗口，输入命令：cmd 并回车进入命令行窗口，接着输入命令：ipconfig/flushdns 回车后执行刷新本地 DNS 缓存数据即可。

