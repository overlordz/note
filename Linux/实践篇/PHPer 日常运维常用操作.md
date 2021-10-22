> 如果是直接通过 Git 更新代码，请忽略该文章

### 备份

> 某个项目备份，并剔除不要备份的目录或文件

```
tar -czvf /data/itemBackup/2021-10-9-mp-master.tar.gz /data/wwwroot/mp-master --exclude=/data/wwwroot/mp-master/.git --exclude=/data/wwwroot/mp-master/vendor --exclude=/data/wwwroot/mp-master/storage
```



### 打包文件

> 当前分支对比某个提交的code进行对比，并生成对应的压缩包（7z、zip）

```
# zip压缩包，要确定已安装zip
git diff d3e0ecd b916f17 --name-only | xargs zip 2021-10-9-mp.zip
# tar压缩包
git diff d3e0ecd b916f17 --name-only | xargs tar -zcvf 2021-10-9-mp.tar.gz
```

##### 

### 解压、并覆盖文件

```
tar -xzvf 2021-10-13-mp.tar.gz -C /data/wwwroot/mp-master
```
