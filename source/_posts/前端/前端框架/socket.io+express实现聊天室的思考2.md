---
layout: pages
title: 使用socket.io和node.js将消息发送到特定客户端（二）
date: 2018-08-20 14:05:03
tags:
      - 前端框架
categories: 前端
---
# 优化聊天室的一些新Get的技巧
## 调色盘调用

```html
<input type="color" placeholder="#000" title="font color">
```
效果图如下：
![调色盘](socket.io+express实现聊天室的思考2/调色盘.gif)

## 打开新Tab页面图片预览

```js
_DisplayImage:(user,imgData,color)=>{
    var userMsg=document.getElementById('recv-area'),
        newMsg=document.createElement('p')
    newMsg.style.color=color||"#000"
    newMsg.innerHTML=user+'<a href="' + imgData + '" target="_blank"><img src="'+imgData+'"></a>'
    userMsg.appendChild(newMsg)
    user.scrollTop=userMsg.scrollHeight  
    
}
```