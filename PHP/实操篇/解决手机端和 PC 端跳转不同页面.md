### NGINX 检测跳转

如果是手机端和 pc 端需要跳转不同的页面，则需要在 nginx.conf 中的 location / {} 中加入如下代码:

```
if ($http_user_agent ~* '(Android|webOS|iPhone|iPod|BlackBerry)') {
    rewrite ^.+ http://127.0.0.1:8080/mobile/$uri;
}
```

其中 mobile 为手机端的路径标识，可以自定义修改.



### JavaScript 检测跳转

```
<script>
    //H5移动版自适应跳转js
    var mobileAgent = new Array("iphone", "ipod", "ipad", "android", "mini", "mobile", "mobi", "mqqbrowser", "blackberry", "webos", "incognito", "webmate", "bada", "nokia", "symbian", "wp7", "wp8", "lg", "ucweb", "skyfire");
    var browser = navigator.userAgent.toLowerCase();
    var _tag = "{$_GET['tag']}";
    if(_tag != 'web'){
        for (var i = 0; i < mobileAgent.length; i++) {
            if (browser.indexOf(mobileAgent[i]) != -1) {
                window.location.href = 'http://www.xxx.com';
                break;
            }
        }
    }
</script>
```

