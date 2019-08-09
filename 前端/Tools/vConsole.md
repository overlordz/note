#### Github官方地址：

- <https://github.com/Tencent/vConsole/blob/dev/doc/tutorial_CN.md>



#### `npm` 安装

```
npm install vconsole
```



使用webpack，然后js代码中

```
import VConsole from 'vconsole/dist/vconsole.min.js' //import vconsole
let vConsole = new VConsole() // 初始化
```



或者找到这个模块下面的 `dist/vconsole.min.js` ，然后复制到自己的项目中

```
<head>
    <script src="dist/vconsole.min.js"></script>
</head>
<!--建议在 `<head>` 中引入哦~ -->
<script>
  // 初始化
  var vConsole = new VConsole();
  console.log('VConsole is cool');
</script>
```

