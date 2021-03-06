# websocket简介
## websocket是什么
答: 它是一种网络通信协议，是 `HTML5` 开始提供的一种在单个 TCP 连接上进行全双工通讯的协议。
### 为什么需要websocket? 疑问? 我们已经有了 HTTP 协议，为什么还需要另一个协议？它能带来什么好处？
答:
+ 因为 HTTP 协议有一个缺陷：通信只能由客户端发起
+ 我们都知道轮询的效率低，非常浪费资源（因为必须不停连接，或者 HTTP 连接始终打开）, 因此websocket应运而生。
## 简介
WebSocket用于在Web浏览器和服务器之间进行任意的双向数据传输的一种技术。WebSocket协议基于TCP协议实现，包含初始的握手过程，以及后续的多次数据帧双向传输过程。其目的是在WebSocket应用和WebSocket服务器进行频繁双向通信时，可以使服务器避免打开多个HTTP连接进行工作来节约资源，提高了工作效率和资源利用率。
![](http://www.ruanyifeng.com/blogimg/asset/2017/bg2017051502.png)

WebSocket目前支持两种统一资源标志符`ws`和`wss`，类似于HTTP和HTTPS。
## 实现原理
浏览器发出webSocket的连线请求，服务器发出响应，这个过程称为`握手`,握手的过程只需要一次，就可以实现持久连接。

## 握手与连接

浏览器发出连线请求,此时的request如下：

![request](http://upload-images.jianshu.io/upload_images/4060631-482f8d79d35b6616.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


通过`get`可以表明此次连接的建立是以HTTP协议为基础的，返回101状态码。

**如果不是101状态码，表示握手升级的过程失败了**

101是`Switching Protocols`,表示服务器已经理解了客户端的请求，并将通过Upgrade 消息头通知客户端采用不同的协议来完成这个请求。在发送这个响应后的空档，将`http`升级到`webSocket`。

其中`Upgrade`和`Connection`字段告诉服务端，发起的是`webSocket`协议

`Sec-WebSocket-Key`是浏览器经过Base64加密后的密钥，用来和`response`里面的`Sec-WebSocket-Accept`进行比对验证

`Sec-WebSocket-Version`是当前的协议版本

`Sec-WebSocket-Extensions`是对`WebSocket`的协议扩展


服务器接到浏览器的连线请求返回结果如下：

![response](http://upload-images.jianshu.io/upload_images/4060631-c1dcb04866442d15.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


`Upgrade`和`Connection`来告诉浏览器，服务已经是基于webSocket协议的了，让浏览器也遵循这个协议

`Sec-WebSocket-Accept`是服务端确认后并加密后的`Sec-WebSocket-Accept`

至此，webSocket连接成功，接下来就是`webSocket`的协议了。


## 客户端的简单示例
```jS
var ws = new WebSocket("wss://echo.websocket.org");

ws.onopen = function(evt) {
  console.log("Connection open ...");
  ws.send("Hello WebSockets!");
};

ws.onmessage = function(evt) {
  console.log( "Received Message: " + evt.data);
  ws.close();
};

ws.onclose = function(evt) {
  console.log("Connection closed.");
};

ws.onerror = function(evt) {
  console.log("error!!!");
};
```
## 客户端的 API
+ 以下 API 用于创建 WebSocket 对象
```js
var ws = new WebSocket('ws://echo.websocket.org');
```
+ websocket 属性


```
ws.readyState
CONNECTING：值为0，表示正在连接。
OPEN：值为1，表示连接成功，可以通信了。
CLOSING：值为2，表示连接正在关闭。
CLOSED：值为3，表示连接已经关闭，或者打开连接失败。

ws.bufferedAmount
只读属性 bufferedAmount 已被 send() 放入正在队列中等待传输，但是还没有发出的 UTF-8 文本字节数。
```
+ WebSocket 事件

```
事件       事件处理程      描述
open     ws.onopen      连接建立时触发
message  ws.onmessage   客户端接收服务端数据时触发
error    ws.onerror     通信发生错误时触发
close    ws.onclose     连接关闭时触发
```
如果要指定多个回调函数，可以使用addEventListener方法
```js
ws.addEventListener('open', function (event) {
  ws.send('Hello Server!');
});
ws.addEventListener("close", function(event) {
  ...
  // handle close event
});
ws.addEventListener("message", function(event) {
  var data = event.data;
  ...
  // 处理数据
});
```
+ websocket 方法
```
方法                  描述
ws.send()            使用连接发送数据
ws.close()           关闭链接
```
# node 搭建服务器

## ws模块
[ws](https://github.com/websockets/ws)是一个websocket库，可以用了创建服务器。

新建server.js 输入以下代码
``` js
const WebSocket = require('ws');
 
const wss = new WebSocket.Server({ port: 8181 });
 
wss.on('connection', function connection(ws) {
  ws.on('message', function(message){
    wss.clients.forEach(function(ws){ // 看这里看这里  wss.clients 拿到所有的连接
          ws.send(message) // 发送消息
      })
  })
});

```
新建index.html  这里只列举下js代码

```html
<script>
var ws = new WebSocket('ws://localhost:8181')
ws.onopen = function(e){
    console.log('开始连接服务器')
    $('.btn-primary').click(function(){
        ws.send($('.form-control').val()) // 发送消息
      })
      ws.onmessage = function(msg){ // 监听消息
        $('body').append('<p>'+ msg.data +'</p>')
      }
      $('.btn-danger').click(function(){
        ws.close()  //  关闭连接
      })
}
</script>
```
完整代码请戳[github](https://github.com/Ortonzhang/WebSocket-ws)

这里使用ws模块实现了两个demo，运行`server.js`后请自行查看。


# Socket.io

## 简介
Socket.io是一个`webSocket`库，目标是构建不同浏览器和移动设备上使用的实时应用。它会自动根据浏览器从`webSocket` `ajax长轮询` `ifrane流`等各种方式选择最佳的方式。

## 特点
Socket.io主要有以下几点：

 1、实时分析：将数据推送到客户端，这些客户端会被表示为实时计数器，图表或日志客户
 2、实时通讯和聊天：几行代码就可以实现一个简单的聊天室
 3、二进制流传输：支持任何形式的二进制文件传输，例如：图片，视频，音频等
 4、文档合并：允许多个用户同时编辑一个文档，并且能够看到每个用户做出的修改

## 聊天室的实现

[Socket.io](https://socket.io/docs/)上面有个入门的聊天室demo，基于`node-http-server`或者`express`,玩了`koa`以后，觉得`koa`很清爽，所以打算用`koa`来实现聊天室。

首先创建`simple-koa-chat`文件夹，用来存放我们的代码。

执行`npm init -y`命令生成package.json文件

执行`npm i koa socket.io -D` 安装koa和socket.io，并添加到`devDependencies`依赖

执行`mkdir src && cd src && touch index.html`创建src文件夹，并在里面创建index.html

执行`cd../ && touch app.js`，回到根目录下，创建app.js。

执行完后，在编辑器里面打开，此时目录结构如下：

![目录结构](http://upload-images.jianshu.io/upload_images/4060631-5ea625024b49c2c3.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

编辑app.js文件
```js

	const Koa = require('koa')
	const app = new Koa()
	const fs = require('fs')
	const server = require('http').Server(app.callback())
	const io = require('socket.io')(server)


	app.use(async (ctx, next)=>{
		ctx.response.type = 'html'
		ctx.body = await fs.createReadStream('./src/index.html')
	})

	server.listen(8080, ()=>{
		console.log('open http://localhost:8080')
	});
	io.on('connection', function(socket){
		socket.on('chat message', function(msg){
			io.emit('chat message', mas)
		})
	})
```

编辑index.html

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>Socket.IO chat</title>
    <link rel="stylesheet" href="https://cdn.bootcss.com/bootstrap/3.3.7/css/bootstrap.min.css">
  </head>
  <style>
    li{
      padding: 4px 10px;
      border: 1px solid #eee;
      width:fit-content;
      border-radius: 5px;
      margin-bottom: 10px;
    }
  </style>
  <body>
    <div class="container" style="margin-top:100px">
        <div class="panel panel-default">
            <div class="panel-heading">Socket.IO 简易聊天室</div>
            <div class="panel-body">
                <div class="col-md-12">
                    <ul class="messages pull-letf list-unstyled"></ul>
                    <form action="">
                      <div class="form-group">
                        <textarea class="form-control"  placeholder="输入发言内容"></textarea>
                      </div>
                      <button type="button" class="btn btn-primary">发言</button>
                    </form>
                </div>
            </div>
          </div>
    </div>


    <script src="/socket.io/socket.io.js"></script>
    <script src="https://code.jquery.com/jquery-1.11.1.js"></script>
    <script>
        $(function () {
            var socket = io();
            $('.btn').click(function(){
              socket.emit('chat message', $('.form-control').val());
              $('.form-control').val('');
            });

            // 监听 chat message事件
            socket.on('chat message', function(msg){
              $('.messages').append($('<li>').text(msg));
            });
        });
    </script>
  </body>
</html>

```

执行`npm run start` 启动我们的服务。


打开浏览器你可以看到如下的页面

![简易聊天室](https://github.com/Ortonzhang/Socket-chat-simple/raw/master/images/gif.gif)

## Socket.io API

`Socket.io`由两部分组成：

1、服务端  挂载或集成到nodeJS http服务器  `socket.io`

2、客户端 加载到浏览器的客户端 `socket.io-client`


先来说下服务端集成，分为简单的两步：

1、引入模块并实例化
```js
// 这里使用koa框架，其他框架原理都一样

const Koa = require('koa')
const app = new Koa()

const http = require('http').Server(app.callback())
const io = require('socket.io')(http)

// 引入`koa`并且初始化，引入`http`模块，将`koa`的回调当作`http.Server`的回调函数，最后将http传入实例化一个`socket.io`。
```

2、设置连接后的操作
```js
io.on('connection', function(socket){
  socket.on('chat', function(msg){
    socket.emit('client', msg)
  })
})

// io.on函数接收'connection'字符串做为客户端发起连接的事件，连接成功后，调用带有		socket参数的回调函数。接收一个chat自定义的事件，使用socket.emit方法发送消息

```
服务端集成好后，接下来是客户端

在`</body>`标签中添加以下代码
```html
<script src="/socket.io/socket.io.js"></script>
<script>
  var socket = io()

  socket.emit('chat', 'hello')

  socket.on('client', function(data){
    console.log(data)
  })
</script>

  <!-- 加载客户端的js文件，调用io() 函数, 初始化socket对象 发送chat事件到服务端，这时候服务端接收到了chat事件，并发出了client事件，浏览器接收到了client事件，将数据打印到了控制台上。 -->
```


流程大概如下：
![流程](https://github.com/Ortonzhang/Socket-chat-simple/raw/master/images/emit.jpg)￼


我给你，你给我，来而不往非礼也，哲学啊。


### emit和on函数

通过上图可以看到，每端总会接收事件和发送事件，socket最关键的就是emit和on两个函数，所有的交互其实就是这两个函数的情感纠葛，你来我往。

emit用来发送一个事件（事件名称用字符串表示），名称可以自定义也可使用默认的事件名称，接着是一个对象，表示发送的内容，如：`socket.emit('chat', {'name':'zhangsan'})`。

on用来接收一个事件（事件名称用字符串表示），然后是响应事件的回调函数，其中函数里面的参数就是收到的数据。如`socket.on('chat',function(data){console.log(data)})`。


### 服务端默认事件一览

`io.on('connection', function(socket){})`  socket连接成功时触发，用于初始化

`socket.on('message', function(data, callback){})` 接收客户端通过socket.send传送的消息，data是传输的消息，callback是收到消息后要执行的函数

`socket.on('anything', function(data){})` 收到任何事件都会触发

`socket.on('disconnect', function(){})` socket失去链接时触发，包括关闭浏览器，主动断开，掉线等情况

### 进阶

1、怎么实现私聊？

每个socket都会有一个唯一的id,私聊的实现方式就是找到这个socket对象，发送事件，浏览器接收事件就实现了私聊。

现在有A、B两个链接，B想发送给A，我们拿到A的id告诉服务器，我要发送给A，浏览器从socket数组里面找到这个对应的socket，然后发送事件。

```js
// client
var obj = {
  toKey:'A的Id'，
  message:'我想告诉A'
}
socket.emit('sendOne', obj)

//server
// 这里我们借助underscore库

socket.on('sendOne', function(obj){
  var ToSocket = _.findWhere(io.sockets.sockets, {id:obj.id}) // 看这里看这里
  ToSocket.emit('toOne', obj)
})

//
socket.on('toOne', function(obj){
  // 这里写自己的逻辑 obj就是B私聊给A的信息
})
```

参考上面API，我们可以将聊天室一步步的丰富起来，添加更多的功能，最后它大概长这样
![](https://github.com/Ortonzhang/Socket-chat-simple/raw/master/images/gif1.gif)

[代码请戳](https://github.com/Ortonzhang/Socket-chat-simple)

简易聊天室在`master`分支，丰富后的聊天室在`zjx`分支，请自行查看


## 服务器端的实现
### 常用的 Node 实现有以下三种
+ [µWebSockets](https://github.com/uNetworking/uWebSockets)
+ [Socket.IO](http://socket.io/)
+ [WebSocket-Node](WebSocket-Node)