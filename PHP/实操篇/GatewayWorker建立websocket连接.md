#### 使用workman 中的 GatewayWorker 发起聊天

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<meta name="viewport" content="width=device-width, initial-scale=1.0">
	<title>Document</title>
</head>
<body>
	<script src="http://code.jquery.com/jquery-2.1.1.min.js"></script>
	<script>
		/**
		 * 与GatewayWorker建立websocket连接，域名和端口改为你实际的域名端口，
		 * 其中端口为Gateway端口，即start_gateway.php指定的端口。
		 * start_gateway.php 中需要指定websocket协议，像这样
		 * $gateway = new Gateway(websocket://0.0.0.0:7272);
		 */
		ws = new WebSocket("ws://test.website.cn:8900");
		// 服务端主动推送消息时会触发这里的onmessage
		ws.onmessage = function(e){
		    // json数据转换成js对象
		    var data = eval("("+e.data+")");
		    var type = data.type || '';
		    switch(type){
		        // Events.php中返回的init类型的消息，将client_id发给后台进行uid绑定
		        case 'init':
		        console.log(data);
		            // 利用jquery发起ajax请求，将client_id发给后端进行uid绑定
		            // $.post('./bind.php', {client_id: data.client_id}, function(data){}, 'json');
		            break;
		        // 当mvc框架调用GatewayClient发消息时直接alert出来
		        default :
		            console.log(e.data);
		    }
        };
	</script>
</body>
</html>
```

