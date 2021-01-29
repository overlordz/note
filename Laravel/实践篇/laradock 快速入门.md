

#### 错误处理

```shell
Computing checksum with sha256sum
Checksums matched!
npm notice 
npm notice New minor version of npm available! 7.4.3 -> 7.5.0
npm notice Changelog: <https://github.com/npm/cli/releases/tag/v7.5.0>
npm notice Run `npm install -g npm@7.5.0` to update!
npm notice 
Now using node v15.7.0 (npm v7.4.3)
Creating default alias: default -> node (-> v15.7.0)
Now using node v15.7.0 (npm v7.4.3)
node -> stable (-> v15.7.0) (default)
npm WARN deprecated urix@0.1.0: Please see https://github.com/lydell/urix#deprecated
npm WARN deprecated resolve-url@0.2.1: https://github.com/lydell/resolve-url#deprecated
npm WARN deprecated fsevents@1.2.13: fsevents 1 will break on node v14+ and could be using insecure binaries. Upgrade to fsevents 2.
npm WARN deprecated chokidar@2.1.8: Chokidar 2 will break on node v14+. Upgrade to chokidar 3 with 15x less dependencies.
npm ERR! code EAI_AGAIN
npm ERR! syscall getaddrinfo
npm ERR! errno EAI_AGAIN
npm ERR! request to https://registry.npmjs.org/spdx-correct/-/spdx-correct-3.1.1.tgz failed, reason: getaddrinfo EAI_AGAIN registry.npmjs.org

npm ERR! A complete log of this run can be found in:
npm ERR!     /home/laradock/.npm/_logs/2021-01-29T03_31_49_139Z-debug.log
The command '/bin/sh -c if [ ${INSTALL_NODE} = true ]; then     mkdir -p $NVM_DIR &&     curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash         && . $NVM_DIR/nvm.sh         && nvm install ${NODE_VERSION}         && nvm use ${NODE_VERSION}         && nvm alias ${NODE_VERSION}         && npm config set fetch-retries ${NPM_FETCH_RETRIES}         && npm config set fetch-retry-factor ${NPM_FETCH_RETRY_FACTOR}         && npm config set fetch-retry-mintimeout ${NPM_FETCH_RETRY_MINTIMEOUT}         && npm config set fetch-retry-maxtimeout ${NPM_FETCH_RETRY_MAXTIMEOUT}         && if [ ${NPM_REGISTRY} ]; then         npm config set registry ${NPM_REGISTRY}         ;fi         && if [ ${INSTALL_NPM_GULP} = true ]; then         npm install -g gulp         ;fi         && if [ ${INSTALL_NPM_BOWER} = true ]; then         npm install -g bower         ;fi         && if [ ${INSTALL_NPM_VUE_CLI} = true ]; then         npm install -g @vue/cli         ;fi         && if [ ${INSTALL_NPM_ANGULAR_CLI} = true ]; then         npm install -g @angular/cli         ;fi         && ln -s `npm bin --global` /home/laradock/.node-bin ;fi' returned a non-zero code: 1
ERROR: Service 'workspace' failed to build
```



**原因** : raw.githubusercontent.com域名的DNS解析被污染,无法获取正确的ip地址

**解决方案**:

方法一：

1. 打开https://www.ipaddress.com/ 输入域名,获取到正确的**ip**

2. 在本机的 host 文件中添加(linux系统: sudo vi /etc/hosts)

   ```
   199.232.28.133 raw.githubusercontent.com
   199.232.28.133 user-images.githubusercontent.com
   199.232.28.133 avatars2.githubusercontent.com
   199.232.28.133 avatars1.githubusercontent.com
   ```

方法二：

如果可以不使用 `npm` 的话可以把 `.env` 文件中的 (全部设置false)：

```
WORKSPACE_NVM_NODEJS_ORG_MIRROR=https://npm.taobao.org/mirrors/node 
WORKSPACE_INSTALL_NODE=false
WORKSPACE_NODE_VERSION=node
WORKSPACE_NPM_REGISTRY=
WORKSPACE_INSTALL_PNPM=false
WORKSPACE_INSTALL_YARN=false
WORKSPACE_YARN_VERSION=latest
WORKSPACE_INSTALL_NPM_GULP=false
WORKSPACE_INSTALL_NPM_BOWER=false
WORKSPACE_INSTALL_NPM_VUE_CLI=false
WORKSPACE_INSTALL_NPM_ANGULAR_CLI=false
```



### laradock 运行报错 Service 'workspace' failed to build:

```
curl: (7) Failed to connect to raw.githubusercontent.com port 443: Connection refused
/bin/sh: 1: .: Can't open /home/laradock/.nvm/nvm.sh
ERROR: Service 'workspace' failed to build: The command '/bin/sh -c if [ ${INSTALL_NODE} = true ]; then  
```

方法一：

```
$ docker-compose build --no-cache workspace
```

方法二：

```
$ docker-compose stop`
$ docker-compose build workspace
```

GWF 的问题，在 www.ipaddress.com/
查询 raw.githubusercontent.com 的真实 IP,
然后手工添加到 hosts 文件:
199.232.28.133 raw.githubusercontent.com

也可以将 .env 中的时区

```
WORKSPACE_TIMEZONE=UTC
```

从格林威治 改为 中国

```
WORKSPACE_TIMEZONE=PRC
```



### mysql容器无法启动(确认端口没有被占用) ,log报错 InnoDB: Table flags are 0 in the data dictionary but the flags in file ./ibdata1 are 0x4800!

**原因** : laradock数据目录写入问题

**解决方案**:

1. 清空~/.laradock/data/mysql文件夹
2. 修改.env文件.DATA_PATH_HOST=~/.laradock/data  为: DATA_PATH_HOST=./data(在当前目录新建data文件夹)

二选一,之后重启容器:

```shell
docker-compose up -d mysql
```



### mysql/redis等启动成功但连接失败

**解决方案**:
laravel项目中的.env文件中作如下修改:

```
DB_HOST=127.0.0.1 改成 DB_HOST=mysql #容器名

REDIS_HOST=127.0.0.1 改成 REDIS_HOST=redis
```



待整理：https://segmentfault.com/a/1190000021076776