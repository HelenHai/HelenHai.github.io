---
layout: pages
title: socket.io+express实现聊天室的思考（一）
date: 2018-08-17 14:05:03
tags:
categories: 前端
---
本文默认你已掌握 node 相关基础知识

GitHub地址：https://github.com/HelenHai/node-chat

# 前导知识
## webSocket协议
是区别是 HTTP 的一种全双工通信协议，也就是说服务器可以主动推送信息到客户端。
但是需要注意的一点是： webSocket协议是建立在HTTP协议基础之上的，需要经过一次握手。所以连接的发起方仍是客户端 。
## express
Express 是一个简洁而灵活的 node.js Web应用框架, 提供一系列强大特性帮助你创建各种Web应用。具体点讲，Express框架建立在node.js内置的http模块上，通过封装HTTP模块，让开发者更加便捷的去搭建服务器
## socket.io
Node.js中使用socket的一个包。使用socket.io可以很方便的建立服务器到客户端的sockets连接。
socket.io分为两部分：
1. 在浏览器中运行的客户端库
2. 面向node.js的服务端库

而 `emit` 和 `on`可以说是socket.io的核心，通过 emit 和 on 可以实现服务器和客户端之间的通信
#整体思路
客户端将信息发送至服务器端，服务端再将这些信息广播到客户端。这也是为什么要采用`webSocket` 协议来实现聊天室的原因。

实现一个简单的聊天室，我们需要实现以下内容：
【聊天模式】
1. 群聊
2. 独聊

【必需功能点】
1. 发送文字
2. 发送图片（或者文件）
3. 发送emoji表情

【附加功能点】
1. 自主选择文字颜色
2. 显示在线用户列表
3. 清屏

在架构这个项目时最好根据面向对象思想，符合UNIX哲学去设计。所以我们可以定义一个全局变量初始化整个项目，定义一个 Chat 类用于整个程序的开发
```js
window.onload=function(){
    var chat = new Chat()
    chat._Init() 
}
```
```js
var Chat = function(){
    this.socket = null
}
```
之后我们向 Chat 原型添加业务方法
```js
_Init()                             //初始化
_InitialEmoji()                     //初始化emoji表情
_DisplayNewMsg(user, msg, color)    //显示发送过来的消息
_DisplayImage(user, imgData, color) //显示图片
_ShowEmoji(msg)                     //显示emoji表情

_DisplayOneMsg(user,msg,color)      //实现独聊
```
# 前后端通信及Chat原型的实现
### （客户端）chat.js
以下代码定义了整个程序都要使用的Chat类，**之后的处理消息等所有业务逻辑都写在这个类中**
```js
Chat.prototype={
    _Init:()=>{
        var that=this
        //关键！建立到服务器的连接
        this.socket=io.connect()    
        //监听socket的connect事件，此事件表示连接已建立
        this.socket.on('connect',function(){
            //....
        })
    }
    
    //其他业务逻辑
    _InitialEmoji()                     //初始化emoji表情
    _DisplayNewMsg(user, msg, color)    //显示发送过来的消息
    _DisplayImage(user, imgData, color) //显示图片
    _ShowEmoji(msg)                     //显示emoji表情
    
    _DisplayOneMsg(user,msg,color)      //实现独聊
}
```
### （服务端）server.js
```js
const express = require('express'),
      app = express(),
      server = require('')
      io = require('socket.io').listen(server)

//创建一个数组保存在线所有用户昵称
var users = []
//存储在线用户
var socketList = {}

app.use('/', express.static(__dirname + '/pages'))
server.listen(3000, function () { 
  console.log('服务器已成功启动')
})  

//socket.io绑定到客户端的连接
io.sockets.on('connection',function(socket){

//通过socket.on()来监听客户端事件
})
```
# 参考资料
【聊天室demo】http://www.cnblogs.com/Wayou/p/hichat_built_with_nodejs_socket.html
【socket.io文档】https://socket.io
【express文档】http://expressjs.com/en/4x/api.html

