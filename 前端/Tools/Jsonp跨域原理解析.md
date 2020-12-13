## jsonp跨域原理解析

### 1、背景：

由于浏览器`同源策略`的限制，非同源下的请求，都会产生跨域问题，jsonp即是为了解决这个问题出现的一种简便解决方案。

### 2、同源策略：

> 同一协议，同一域名，同一端口号。当其中一个不满足时，我们的请求即会发生跨域问题。
> 举个栗子🌰：

```
http://www.abc.com:3000到https://www.abc.com:3000的请求会出现跨域（域名、端口相同但协议不同）
http://www.abc.com:3000到http://www.abc.com:3001的请求会出现跨域（域名、协议相同但端口不同）
http://www.abc.com:3000到http://www.def.com:3000的请求会出现跨域（域名不同）
```



### 3、突破同源策略限制：

现在知道了`同源策略`，那我们就来看下`jsonp`是如何突破`同源策略`的限制实现跨域的

首先，不知道大家有没有注意，不管是我们的script标签的src还是img标签的src，或者说link标签的href他们没有被同源策略所限制，比如我们有可能使用一个网络上的图片，就可以请求得到，jsonp即是利用这一特性实现的(这也就是为什么jsop只支持get请求的原因)。

```
<img src="https://ss3.baidu.com/9fo3dSag_xI4khGko9WTAnF6hhy/image/h%3D300/sign=6d0bf83bda00baa1a52c41bb7711b9b1/0b55b319ebc4b745b19f82c1c4fc1e178b8215d9.jpg">
```

src或href链接的静态资源，本质上来说也是一个get请求，拿csdn上的静态资源举例：

<img src="https://image-static.segmentfault.com/165/546/165546193-5eec1a08297bd_articlex">



可以看到，确实是个get请求无疑。同理img标签的src和link标签的href也会发送一个get请求去请求静态资源。那么我们通过这点，是不是发现了点什么，这些标签的src和link属性，并没有受同源策略的限制。说到这里jsonp的实现原理就浮出水面了。

jsonp就是使用通源策略这一“漏洞”，实现的跨域请求（这也是jsonp跨域只能用get请求的原因所在）。想象一下，既然是个get请求，那么服务端一定可以接收到，并做出反馈。ok，知道这两点之后，我们开始具体使用jsonp进行跨域请求。

### 4、JSONP跨域实现：

根据上边所说的，我们要用过利用img、srcipt，link标签的src或href属性（到底使用那个标签无所谓）来实现，那么我们如何做呢，我们来看一段简单的代码，为了方便，我这里使用jQuery：

```
$('#btn').click(function(){
    var frame = document.createElement('script');
    frame.src = 'http://localhost:3000/article-list?name=leo&age=30&callback=func';
    $('body').append(frame);
});
```


可以看到，让我们点击按钮的时候，创建了一个script标签(即会发送一个get请求到src指向的地址)，src地址是 "`localhost:3000/article-list`" ,这个src地址，就是我们请求的服务端接口。注意，这里我们有是那个参数，name,age和callback，name和age不说了，这跟我们平时普通的get请求参数无异。主要说下callback这个参数，callback参数就是核心所在。为什么要定义callback呢？首先我们知道，这个get请求已经被发出去了，那么我们如何接口请求回来的数据呢，callback=func则可以帮我们做这件事。我们继续看下边的代码。

```
<button id="btn">点击</button>

  <script src="https://cdn.bootcss.com/jquery/3.3.1/jquery.min.js"></script>
  <script>
    $('#btn').click(function(){
        var frame = document.createElement('script');
        frame.src = 'http://localhost:3000/article-list?name=leo&age=30&callback=func';
        $('body').append(frame);
    });

    function func(res){
        alert(res.message+res.name+'你已经'+res.age+'岁了');
    }
  </script>
```




我们声明了一个func函数，但没有执行，你可以想一下，如果服务端接口到get请求，返回的是**func({message:'hello'})，这样的话在服务端不就可以把数据通过函数执行传参的方式实现数据传递了吗**。

### 4、服务端代码实现：

服务端代码，这里使用的是express，这里不再对express做具体介绍。代码如下：

```
router.get('/article-list', (req, res) => {
  console.log(req.query, '123');
  let data = {
    message: 'success!',
    name: req.query.name,
    age: req.query.age
  }
  data = JSON.stringify(data)
  res.end('func(' + data + ')');
});
```


ok，接下来当我们点击提交的时候，就获取到了服务端反回的数据，这样下来，就完成了实现jsonp的跨域，如下结果：

![](https://image-static.segmentfault.com/154/612/1546123382-5eec1b7258ba1_articlex)



### 总结：

需要注意的是，callback参数定义的方法是需要前后端定义好的，具体什么名字，商讨好就可以了。其实jsonp的整个过程就类似于前端声明好一个函数，后端返回执行函数。执行函数参数中携带所需的数据，整个过程实际非常简单易懂。




文章转至：https://segmentfault.com/a/1190000022972376

