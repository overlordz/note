### # 对文件做MD5校验

在命令窗口执行

> CertUtil -hashfile C:\xxx.tar MD5
> 此命令不仅可以做MD5哈希算法校验，还支持其他的哈希算法，具体如下：
> CertUtil -hashfile 文件路径 [算法]
> 支持的算法有：MD2   MD4  MD5  SHA1  SHA256  SHA384  SHA512



### # windows常用软件一键启动

##### 1、新建一个记事本

##### 2、选择要打开程序的快捷方式或.exe位置

##### 3、完成记事本编辑

```
脚本格式： start+一个空格+""(英文,中间无空格)+一个空格+"目标程序路径"
脚本例子：start "" "D:\Program Files\WeChat\WeChat.exe"
```
##### 4、保存，文件扩展名修改为.bat

##### 补充说明

```
担心电脑配置不行，启动多个程序怕卡，可以设置时间延迟启动

    start "" "C:\Program Files (x86)\Google\Chrome\Application\chrome.exe"&ping localhost -n 3
    &可以加也可以不加， 表示程序顺序执行，ping localhost -n 1表示1秒时间延迟启动
```