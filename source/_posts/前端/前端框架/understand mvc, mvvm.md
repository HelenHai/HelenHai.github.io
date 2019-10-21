---
layout: pages
title: 理解MVC , MVVM
date: 2018-02-16 22:04:03
tags:
      - 前端框架 
categories: 前端
---

在整理这篇博客之前，觉得自己了解这两种架构。后来仔细想了想，好像是什么也没搞清楚，让我讲讲区别好像还真讲不出来。今天特意花点时间来整理一下 😄  在学校期间接触的基本都是MVC架构了，之前也有了解一点ThinkPHP ，所以对MVC了解的稍微多一些。
# MVC
提到MVC首先想到 View, Controller, Model 这三个单词，每个部分之间的通信可以用下图来表示（盗用一下阮老师的图）
![bg2015020105](https://user-images.githubusercontent.com/17962385/30517666-0af22e02-9b99-11e7-9fcc-2d66cc57f51d.png)
1. View通过DOM操作将指令传送到Controller
2. Controller完成逻辑后，要求Model改变状态
3. Model将新的数据发送到View，在这里Model层和View 层直接交互

在MVC架构中，占主导地位的是Controller层，它承担了整个架构中的大部分业务逻辑。**之所以这样设计，MVC中认为界面上每个变化都是一个事件，开发者只需要针对每个事件写一段代码（Controller），来把用户的输入转换成model里的对象就行了。**


# MVVM
![bg2015020110](https://user-images.githubusercontent.com/17962385/30517707-6d7189c8-9b9a-11e7-8623-cd5f93f2fea7.png)

在MVVM 架构中，“View层非常的薄，没有部署逻辑业务。View层和Model层之间不直接发生交互，而是交由 ViewModel 层来处理”。说白了就是： **给view中的控件也定义一个对应的数据对象，然后只要修改这个数据对象，View 中显示的内容也就自动更新。而在View 中做了任何操作，这个数据对象也跟着自动更新**

🌟那么引入viewModel的目的是什么，有什么优点？
 viewModel : 其实就是与视图层 View 对应的Model。:triangular_flag_on_post: **因为数据库结构往往是不能直接跟界面控件一一对应上的，所以需要再定义一个数据对象专门对应 View 上的控件。而viewModel 的职责就是把 model 对象封装成可以显示和接收输入的界面数据对象**

所以在MVVM架构中，就取消了View和Model之间的直接交互而是加入了一个ViewModel层，以Vue为例
![vue](https://user-images.githubusercontent.com/17962385/30517804-11722530-9b9d-11e7-9578-879caa2b27ed.png)
View 和 Model 之间并没有直接的联系，而是通过ViewModel进行交互，Model 和 ViewModel 之间的交互是双向的， 因此View 数据的变化会同步到Model中，而Model 数据的变化也会立即反应到View 上。

ViewModel 通过双向数据绑定把 View 层和 Model 层连接了起来，而View 和 Model 之间的同步工作完全是已经完全由框架封装好，不需要我们去操作。开发者只需要关注业务逻辑就好，也不需要手动去操作DOM，不需要关注数据状态的同步问题。

**MVVM总结其核心就是：实现对视图View 和 模型 Model变动 的监听**

# MVC和MVVM的区别
不过看到这里会有一个疑问，怎么感觉 viewModel 的作用和 MVC中的controller一样？
原理上这两个是差不太多的，但是 viewModel简化甚至剔除了业务逻辑，主要工作是将Model中的数据组装成适合View使用的数据。
而Controller 所要担任的任务更加全面，包括了许多业务逻辑。而对于前端来说，只是很少需要甚至不需要业务逻辑。所以相对MVC架构来讲，个人认为MVVM更胜一筹

