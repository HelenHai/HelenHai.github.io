---
layout: pages
title: 使用socket.io和node.js将消息发送到特定客户端
date: 2018-10-18 14:05:03
tags:
      - 前端框架
categories: 前端
---
原文链接：https://gxnotes.com/article/57622.html

# 问题描述
我正在使用socket.io和node.js，直到现在看起来都不错，但是我不知道如何从服务器发送消息到一个特定的客户端，像这样：
```js
client.send(message, receiverSessionId)
```
但是.send()和.broadcast()方法都没有提供我的需要。
作为一个可能的解决方案，我发现.broadcast()方法接受第二个参数是一个不发送消息的SessionIds数组，所以我可以传递一个数组，所有SessionIds都连接到服务器，除了一个我希望发送消息，但我觉得必须有一个更好的解决方案。
#最佳解决方案
那么你必须抓住客户端(惊喜)，你可以走简单的方式：
```js
var io = io.listen(server);
io.clients[sessionID].send()
```
这可能会破裂，我几乎不怀疑，但是io.clients可能会改变，所以请谨慎使用上述内容

或者您自己跟踪客户端，因此将它们添加到connection侦听器中您自己的clients对象，并将其删除在disconnect侦听器中。

我会使用后者，因为根据您的应用程序，您可能希望在客户端上拥有更多的状态，所以像clients[id] = {conn: clientConnect, data: {...}}这样的工作可能会做这个工作。
# 次佳解决方案
Ivo Wetzel的答案似乎在Socket.io 0.9中似乎没有效果。
简而言之，您现在必须保存socket.id并使用io.sockets.socket(savedSocketId).emit(...)向其发送消息。
这是我在集群Node.js服务器中的工作原理
首先，您需要将Redis store设置为商店，以便消息可以跨越进程：
```js
var express = require("express");
var redis = require("redis");
var sio = require("socket.io");

var client = redis.createClient()
var app = express.createServer();
var io = sio.listen(app);

io.set("store", new sio.RedisStore);


// In this example we have one master client socket 
// that receives messages from others.

io.sockets.on('connection', function(socket) {

  // Promote this socket as master
  socket.on("I'm the master", function() {

    // Save the socket id to Redis so that all processes can access it.
    client.set("mastersocket", socket.id, function(err) {
      if (err) throw err;
      console.log("Master socket is now" + socket.id);
    });
  });

  socket.on("message to master", function(msg) {

    // Fetch the socket id from Redis
    client.get("mastersocket", function(err, socketId) {
      if (err) throw err;
      io.sockets.socket(socketId).emit(msg);
    });
  });

});
```
# 第三种解决方案
一个比迄今为止提出的更简单的解决方案，使用socket.io v1.0.4进行测试：
## server
```js
var
    io = require('socket.io'),
    ioServer = io.listen(8000),
    sequence = 1;
    clients = [];

// Event fired every time a new client connects:
ioServer.on('connection', function(socket) {
    console.info('New client connected (id=' + socket.id + ').');
    clients.push(socket);

    // When socket disconnects, remove it from the list:
    socket.on('disconnect', function() {
        var index = clients.indexOf(socket);
        if (index != -1) {
            clients.splice(index, 1);
            console.info('Client gone (id=' + socket.id + ').');
        }
    });
});

// Every 1 second, sends a message to a random client:
setInterval(function() {
    var randomClient;
    if (clients.length > 0) {
        randomClient = Math.floor(Math.random() * clients.length);
        clients[randomClient].emit('foo', sequence++);
    }
}, 1000);
```
## client
```js
var
    io = require('socket.io-client'),
    ioClient = io.connect('http://localhost:8000');

ioClient.on('foo', function(msg) {
    console.info(msg);
});
```
## 如何运行
安装所需要的库
```bash
npm install socket.io
npm install socket.io-client
```
运行服务器
```js
node server
```
在单独的控制台中运行每个客户端
```js
node client
```
# 第四种方案
每个套接字加入一个名称为套接字号的房间，所以你可以：
```js
io.to(socket#id).emit('hey')
```
【文档】http://socket.io/docs/rooms-and-namespaces/#default-room
# 第五种方案
```js
io.sockets.connected [socketid] .emit();
```
# 第六种方案
您可以使用
```js
//仅发送消息给sender-client
socket.emit('message', 'check this');
//或者您可以发送给包括发件人在内的所有听众
io.emit('message', 'check this');
//发送给发件人以外的所有听众
socket.broadcast.emit('message', 'this is a message');
//或者你可以把它发送到一个房间
socket.broadcast.to('chatroom').emit('message', 'this is the message to all');
```
# 第七种方案
无论我们使用哪个版本，如果我们只是console.log()我们在我们的服务器端nodejs代码中使用的”io”对象， io.on(‘connection’，function(socket){…});]，我们可以看到”io”只是一个json对象，并且有很多子对象，其中存储了套接字id和套接字对象。

我使用的是socket.io版本1.3.5，btw。

如果我们看io对象，它包含，
```js
 sockets:
  { name: '/',
    server: [Circular],
    sockets: [ [Object], [Object] ],
    connected:
     { B5AC9w0sYmOGWe4fAAAA: [Object],
       'hWzf97fmU-TIwwzWAAAB': [Object] },
```
这里我们可以看到这个socket的”B5AC9w0sYmOGWe4fAAAA”等等，所以我们可以做
```js
io.sockets.connected[socketid].emit();
```
再次，进一步的检查，我们可以看到，
```js
 eio:
  { clients:
     { B5AC9w0sYmOGWe4fAAAA: [Object],
       'hWzf97fmU-TIwwzWAAAB': [Object] },

```
所以，我们可以通过这样做从这里检索一个套接字
```js
io.eio.clients[socketid].emit();
```
另外，在发动机下，
```js
engine:
 { clients:
    { B5AC9w0sYmOGWe4fAAAA: [Object],
      'hWzf97fmU-TIwwzWAAAB': [Object] },
```
所以我们也可以写，
```js
io.engine.clients[socketid].emit();
```
所以，我想我们可以通过上面列出的三种方式来实现我们的目标，
1. io.sockets.connected [socketid] .emit();要么
2. io.eio.clients [socketid] .emit();要么
3. io.engine.clients [socketid] .emit();
# 第八种方案
从版本1.4.5开始，请确保在io.to()中提供了一个正确的前缀socketId。我正在使用客户端记录的socketId进行调试，它没有前缀，所以我最终搜索永远，直到我发现！所以你可能要这样做，如果你的Id没有前缀：
```js
io.to('/#' + socketId).emit('myevent', {foo: 'bar'});
```