---
layout: pages
title: 实现一个简单的双向绑定
date: 2017-10-12 16:05:13
tags:
      - browser 
categories: 前端
---
## 摘要 ##
接触Vue有一段时间了，但是对于其双向绑定的实现一直是似懂非懂，今天看到一篇写的比较好的文章 [传送门1](https://ghoskno.github.io/2017/02/21/vue-bind/) 根据原作者的指导自己也去实现了一遍简单的 demo （本文的demo均基于Object.defineProperty 实现数据劫持，利用了对Vue.js实现双向绑定的思想）

[注] 本文所有图片均来自于：[传送门2](https://github.com/DMQ/mvvm#_2)

## 几种主流的双向绑定
1.发布-订阅模式
2.脏值检测
> 通过对比数据是否有变更，来决定是否更新视图。最简单的可以通过定时轮询去检测数据的变动。当然Google不会这么low, Angular 只有在指定事件触发时进入脏值检测：
- DOM事件，比如用户输入文本点击按钮等（ng-click）
- XHR响应事件
- 浏览器 Location 变更
- Timer事件
- 执行 $digidt() 或 $apply()

3.数据劫持
Vue.js 采用的是 `数据劫持+发布/订阅模式` 的方式，通过 `Object.defineProperty()` 来劫持各个属性的 setter/getter， 在数据变动时发布消息给订阅者（Wacther）, 触发相应的监听回调。下图展示了Vue实现双向绑定的流程
![图片描述][1]

# 实现一个简单的双向绑定
```js
<!DOCTYPE html>
<html lang="en">
    <head>
        <title>双向绑定最最最初级demo</title>
        <meta charset="UTF-8">
    </head>
    <body>
        <div id="app">
            <input type="text" id="txt">
            <p id="show-txt"></p>
        </div>
    </body>
    <script>
        var obj={}
        Object.defineProperty(obj,'txt',{
            get:function(){
                return obj
            },
            set:function(newValue){
                document.getElementById('txt').value = newValue
                document.getElementById('show-txt').innerHTML = newValue
            }
        })
        document.addEventListener('keyup',function(e){
            obj.txt = e.target.value
        })
    </script>
</html>
```
# 进阶版demo
DOM操作是非常耗时和好性能，所以在优化过程中先从DOM操作入手。因为遍历解析过程中有多次DOM操作，为了提高性能和效率，需要一种方法来避免对DOM元素的直接封装操作。在Vue使用 `DocumentFragment` 作为替代容器。
`DocumentFragment` 接口表示一个没有父级文件的最小文档对象。它被当做一个轻量版本的Document 使用。所以使用 DocumentFragment 代替DOM直接处理，可以提高性能和速度。

## 封装DOM节点为 DocumentFragment
```js
//将传入 node 的子节点进行劫持，经过处理后重新挂载回目标节点
function convertNode(node,vm){
    var fragment = document.createDocumentFragment(),
        child
    while(child = node.firstChild){
        //将原生节点拷贝到 fragment，并删除之前的child节点
        fragment.appendChild(child)
    }
    return fragment
}

var dom = convertNode(document.getElementById('app'))
document.getElmentById('app').appendChild(dom)
```
## 实现Complie解析模板指令
<!-- ![图片描述](https://image-static.segmentfault.com/111/738/1117380429-57b3110440af0_articlex) -->

Complie 主要做的事情就是解析模板指令，将模板中的变量替换为数据。所以要遍历整个DOM树，进行扫描解析编译，调用对应的指令渲染函数进行渲染，并调用对应的指令更新函数进行绑定
```
<div id="app">
    <input type="text" id="txt" h-model="text">
    {{text}}
</div>
```
```js
function convertNode(node,vm){
    //...
    while(child = node.firstChild){
        Compile(child,vm)
        fragment.appendChild(child)
    }
    return fragment
}
function Compile(node,vm){
    var reg = /\{\{(.*)\}\}/
    if(node.nodeType===1){  
        var attr = node.attributes  //对所有属性进行解析
        for(var i=0;i<attr.length;i++){
            if(attr[i].nodeName=='h-model'){
                //将元素与数据绑定
                var bindName = attr[i].nodeValue
                //为输入框添加事件监听触发
                node.addEventListener('input',function(e){
                    vm.data[bindName] = e.target.value
                    node.value = vm.data[bindName]
                })
                node.removeAttribute('h-model');
            }
            if(node.nodeType===3){
                if(reg.test(node.nodeValue)){
                    var bindName = RegExp.$1.trim()
                    console.log(RegExp.$1)
                    node.nodeValue = vm.data[bindName]
                }
            }
        }
    }
```
## ViewModel 层向 View 层的数据绑定
接下来实现一个 `Xin` 构造器，通过 Compile 来解析模板指令，通过 Observer 监听属性数据的变化实现 `Model` 层向 `View` 层的数据绑定
```js
function Xin(options){
    this.data = options.data
    Observer(this.data,this)
    var id = options.el
    var dom = convertNode(document.getElementById(id),this)
    document.getElementById(id).appendChild(dom)
}
```
新建一个 vm 实例来测试一下 `Model --> View` 的绑定情况
```js
var vm = new Xin({
    el:'app',
    data:{
        text:'Hello MVVM'
    }
})
```
#View 层向 viewModel 层的数据绑定
实际上，在 Observer 中我们已经通过 `数据劫持` 实现了监听每个数据的变化，在控制台打印 `console.log(val)` 就可以实时看到数据的变化。所以接下来实现的关键就是怎么用监听到的数据去更新视图。 
在这里，我们去实现一个 `Wacther` 可以将它理解为观察者 ，他的作用是能够接收从 Observer 发过来的属性变动通知， 然后根据属性的变动更新视图 `update`。

在监听过程中，为所有的 data 属性生成一个主题对象 Dep，Dep中包含需要维护的观察者列表。每当主题对象状态发生变化时，其相关依赖都会得到通知，并且被自动更新（数据变动会触发notify，再调用订阅者的update() 方法）
```js
function Dep(){
    this.subs=[]    //订阅者队列
}
Dep.prototype={
    addSub:function(sub){
        this.subs.push(sub)
    },
    notify:function(){
        this.subs.forEach(function(sub){
            sub.update()
        })
    }
}

funcion Watcher(vm,node,bindName){
    //将全局Dep.target设置为当前页面元素node
    Dep.target = this
    //完成watcher的初始化
    this.name = bindName
    this.node = node
    this.vm = vm

    this.update()    //初次绑定时进行更新
    Dep.target = null    //保证Dep.target唯一
}

Watcher.prototype = {
    get:function(){
        this.value = this.vm.data[this.name]
    },
    update:function(){
        this.get()
        this.node.nodeValue = this.value
    }
}

function Observer(obj,vm){
    //...
    Object.defineProperty(obj,prop,{
        get:function(){...},
        set:function(newVal){
            if(val == newVal) return
            val = newVal
            //data属性被修改，由dep触发view层更新
            dep.notify()
        }
    })
}
```
考虑这样一个问题，什么时候会有双向绑定？ `viewModel --> view` 可能会发生在所有类型的DOM节点上，而 `view --> viewModel` 只能发生在 input, select, textarea 等交互控件上。所以将文本节点包装成 `Watcher` , 添加相关元素的观察者列表中，Watcher 负责更新页面元素
```js
function Compile(node,vm){
    //...
    if(node.nodeType ===3){    //文本节点类型
        if(reg.test(node.nodeValue)){
            var bindName = RegExp.$1.trim()
            new Watcher = (vm,node,bindName)    //为该页面元素node生产watcher
        }
    }
}
```
#更新

本文中实现模板渲染的方法借鉴了 `Vue 1.x` 中实现模板渲染的方法。
`Vue 2.x` 模板渲染 方法借鉴React 中的 **VirtualDOM**，基于 VirtualDOM。 Vue 2.x 还支持服务端渲染SSR


#资料参考
1.https://github.com/DMQ/mvvm#_2


  [1]: /img/bVBQYu
  [2]: /img/bVBQY3