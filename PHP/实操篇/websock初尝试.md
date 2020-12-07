> 原文地址 [mp.weixin.qq.com](https://mp.weixin.qq.com/s/ye7i9HIAj9QQXNIbBTsjrg)

**什么是 WebSocket**

WebSocket 用于在 Web 浏览器和服务器之间进行任意的双向数据传输的一种技术。WebSocket 协议基于 TCP 协议实现，包含初始的握手过程，以及后续的多次数据帧双向传输过程。

其目的是在 WebSocket 应用和 WebSocket 服务器进行频繁双向通信时，可以使服务器避免打开多个 HTTP 连接进行工作来节约资源，提高了工作效率和资源利用率。

**WebSocket 的一些特点：**

*   建立在 TCP 协议之上，服务器端的实现比较容易。

*   与 HTTP 协议有着良好的兼容性。默认端口也是 80 和 443，并且握手阶段采用 HTTP 协议，因此握手时不容易屏蔽，能通过各种 HTTP 代理服务器。

*   数据格式比较轻量，性能开销小，通信高效。

*   可以发送文本，也可以发送二进制数据。

*   没有同源限制，客户端可以与任意服务器通信。

*   协议标识符是 ws（如果加密，则为 wss），服务器网址就是 URL。 

**实现原理**
--------

浏览器发出 webSocket 的连线请求，服务器发出响应，这个过程称为`握手`, 握手的过程只需要一次，就可以实现持久连接。

![](https://mmbiz.qpic.cn/mmbiz_png/QibLP1rpwH8taIxEf8sOHwXhyGt97posDA3BxxBicTz8TnNNKw3UZcX6mlgY2Bl3HugicIJibJFoq4XID9WFs4A72w/640?wx_fmt=png)

**php 实现 websocket 实时消息推送**

这里封装了一个类，具体看 SocketService.php 文件

```php
<?php
class SocketService
{
    // 如果写127.0.0.1，记得只能本机调用才会生效
    private $address = '0.0.0.0';
    private $port = 8083;
    private $_sockets;
    public function __construct($address = '', $port='')
    {
        if(!empty($address)){
            $this->address = $address;
        }
        if(!empty($port)) {
            $this->port = $port;
        }
    }

    public function service(){
        //获取tcp协议号码。
        $tcp = getprotobyname("tcp");
        $sock = socket_create(AF_INET, SOCK_STREAM, $tcp);
        socket_set_option($sock, SOL_SOCKET, SO_REUSEADDR, 1);
        if($sock < 0)
        {
            throw new Exception("failed to create socket: ".socket_strerror($sock)."\n");
        }
        socket_bind($sock, $this->address, $this->port);
        socket_listen($sock, $this->port);
        echo "listen on $this->address $this->port ... \n";
        $this->_sockets = $sock;
    }

    public function run(){
        $this->service();
        $clients[] = $this->_sockets;
        while (true){
            $changes = $clients;
            $write = NULL;
            $except = NULL;
            socket_select($changes, $write, $except, NULL);
            foreach ($changes as $key => $_sock){
                if($this->_sockets == $_sock){ //判断是不是新接入的socket
                    if(($newClient = socket_accept($_sock)) === false){
                        die('failed to accept socket: '.socket_strerror($_sock)."\n");
                    }
                    $line = trim(socket_read($newClient, 1024));
                    $this->handshaking($newClient, $line);
                    //获取client ip
                    socket_getpeername ($newClient, $ip);
                    $clients[$ip] = $newClient;
                    echo "Client ip:{$ip}  \n";
                    echo "Client msg:{$line} \n";
                } else {
                    socket_recv($_sock, $buffer, 2048, 0);
                    $msg = $this->message($buffer);
                    //在这里业务代码
                    echo "{$key} clinet msg:",$msg,"\n";
                    fwrite(STDOUT, 'Please input a argument:');
                    $response = trim(fgets(STDIN));
                    $this->send($_sock, $response);
                    echo "{$key} response to Client:".$response,"\n";
                }
            }
        }
    }

    /**
     * 握手处理
     * @param $newClient socket
     * @return int 接收到的信息
     */
    public function handshaking($newClient, $line){

        $headers = array();
        $lines = preg_split("/\r\n/", $line);
        foreach($lines as $line)
        {
            $line = chop($line);
            if(preg_match('/\A(\S+): (.*)\z/', $line, $matches))
            {
                $headers[$matches[1]] = $matches[2];
            }
        }
        $secKey = $headers['Sec-WebSocket-Key'];
        $secAccept = base64_encode(pack('H*', sha1($secKey . '258EAFA5-E914-47DA-95CA-C5AB0DC85B11')));
        $upgrade = "HTTP/1.1 101 Web Socket Protocol Handshake\r\n" .
            "Upgrade: websocket\r\n" .
            "Connection: Upgrade\r\n" .
            "WebSocket-Origin: $this->address\r\n" .
            "WebSocket-Location: ws://$this->address:$this->port/websocket/websocket\r\n".
            "Sec-WebSocket-Accept:$secAccept\r\n\r\n";
        return socket_write($newClient, $upgrade, strlen($upgrade));
    }

    /**
     * 解析接收数据
     * @param $buffer
     * @return null|string
     */
    public function message($buffer){
        $len = $masks = $data = $decoded = null;
        $len = ord($buffer[1]) & 127;
        if ($len === 126) {
            $masks = substr($buffer, 4, 4);
            $data = substr($buffer, 8);
        } else if ($len === 127) {
            $masks = substr($buffer, 10, 4);
            $data = substr($buffer, 14);
        } else {
            $masks = substr($buffer, 2, 4);
            $data = substr($buffer, 6);
        }
        for ($index = 0; $index < strlen($data); $index++) {
            $decoded .= $data[$index] ^ $masks[$index % 4];
        }
        return $decoded;
    }

    /**
     * 发送数据
     * @param $newClinet 新接入的socket
     * @param $msg  要发送的数据
     * @return int|string
     */
    public function send($newClinet, $msg){
        $msg = $this->frame($msg);
        socket_write($newClinet, $msg, strlen($msg));
    }

    public function frame($s) {
        $a = str_split($s, 125);
        if (count($a) == 1) {
            return "\x81" . chr(strlen($a[0])) . $a[0];
        }
        $ns = "";
        foreach ($a as $o) {
            $ns .= "\x81" . chr(strlen($o)) . $o;
        }
        return $ns;
    }

    /**
     * 关闭socket
     */
    public function close(){
        return socket_close($this->_sockets);
    }
}

$sock = new SocketService();
$sock->run();
```

**前端显示 **

```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width,initial-scale=1, maximum-scale=1, user-scalable=no">
    <title>websocket</title>
</head>
<body>
<input id="text" value="">
<input type="submit" value="send" onclick="start()">
<input type="submit" value="close" onclick="close()">
<div id="msg"></div>
<script>
    /**
     0：未连接

     1：连接成功，可通讯

     2：正在关闭

     3：连接已关闭或无法打开
     */

        //创建一个webSocket 实例
    var webSocket = new WebSocket("ws://192.168.1.208:8004/websocket/demo.php");


    webSocket.onerror = function (event) {
        onError(event);
    };

    // 打开websocket
    webSocket.onopen = function (event) {
        console.log(111);
        onOpen(event);
    };

    //监听消息
    webSocket.onmessage = function (event) {
        console.log(222);
        onMessage(event);
    };


    webSocket.onclose = function (event) {
        onClose(event);
    };

    //关闭监听websocket
    function onError(event) {
        document.getElementById("msg").innerHTML = "<p>close</p>";
        console.log("error" + event.data);
    }

    function onOpen(event) {
        console.log(12312);
        console.log("open:" + sockState());
        document.getElementById("msg").innerHTML = "<p>Connect to Service</p>";
    }

    function onMessage(event) {
        console.log("onMessage");
        document.getElementById("msg").innerHTML += "<p>response:" + event.data + "</p>"
    }

    function onClose(event) {
        document.getElementById("msg").innerHTML = "<p>close</p>";
        console.log("close:" + sockState());
        webSocket.close();
    }

    function sockState() {
        var status = ['未连接', '连接成功，可通讯', '正在关闭', '连接已关闭或无法打开'];
        return status[webSocket.readyState];
    }


    function start(event) {
        console.log(webSocket);
        var msg = document.getElementById('text').value;
        document.getElementById('text').value = '';
        console.log("send:" + sockState());
        console.log("msg=" + msg);
        webSocket.send("msg=" + msg);
        document.getElementById("msg").innerHTML += "<p>request" + msg + "</p>"
    }

    function close(event) {
        webSocket.close();
    }
</script>
</body>
</html>
```

**案例详图如下 **

![](https://mmbiz.qpic.cn/mmbiz_gif/QibLP1rpwH8taIxEf8sOHwXhyGt97posD1tosERINDa5sKo9usH5ojRjcCy0TicuGjsibkj3GfCweViaZf230icHFFA/640?wx_fmt=gif)

