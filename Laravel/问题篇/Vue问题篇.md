> Laravel 版本号：5.8

## Laravel 使用Vue的问题

#### npm run dev 出现失败

问题分析，由于package.json中有cross-env ，这个cross-env是为了window兼容添加的，非window的要把他删掉。


解决步骤如下：

1、删掉node_modules目录，重新执行npm install  或 yarn install

2、删除 package.json 中的 cross-env，其中“devDependencies”对应的cross-env也要删掉。

3、npm run watch-poll 执行查看



如果有以下内容出现：

1、执行 `npm run watch-poll` 的时候如果提示 “Running: yarn add vue-template-compiler --dev --production=false Running: yarn add vue-template-compiler --dev --production=false“，就使用 `yarn add vue-template-compiler --dev --production=false --no-bin-links` 命令安装



**备注：**

建议放弃在windows 环境直接调用npm run  dev  ，错误调试多



参考网站：[点击跳转查看](https://learnku.com/laravel/t/22059)

