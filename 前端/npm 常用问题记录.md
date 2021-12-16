## npm 常用问题记录



##### node-sass 的数据源没设置

```
npm config set sass_binary_site=https://npm.taobao.org/mirrors/node-sass

npm install
```



##### 问题报错内容如下：

```
14 verbose pkgid vue-element-admin@3.10.0
15 verbose cwd D:\Code\dsshop\admin\vue2\element-admin-v3
16 verbose Windows_NT 10.0.19042
17 verbose argv "D:\\nodejs\\node.exe" "D:\\nodejs\\node_modules\\npm\\bin\\npm-cli.js" "run" "dev"
18 verbose node v12.12.0
19 verbose npm  v6.11.3
20 error code ELIFECYCLE
21 error errno 1
22 error vue-element-admin@3.10.0 dev: `cross-env BABEL_ENV=development webpack-dev-server --inline --progress --config build/webpack.dev.conf.js`
22 error Exit status 1
23 error Failed at the vue-element-admin@3.10.0 dev script.
23 error This is probably not a problem with npm. There is likely additional logging output above.
24 verbose exit [ 1, true ]
```



参考链接：https://stackoverflow.com/questions/42308879/how-to-solve-npm-error-npm-err-code-elifecycle

按照下面步骤处理：

1. 清除缓存

   npm cache clean --force

2. 删除目录与文件

   node_modules folder

   package-lock.json

3. 重新安装
   npm install
