---
layout: pages
title: JavaScript几种常用的的继承方式
date: 2018-03-01 18:05:03
tags:
      - JavaScript 
categories: 计算机语言
---
关于 JavaScript 的继承机制其实看了很多博客和文章，但是实际上还是没有去完全理解整个继承机制和 this 做作用。这两块知识一直没有串起来，一直处于一种 ‘懵’ 的状态。所以特意记录一下这块的知识，梳理一下自己的理解 😸 

## 原型链继承
> 每一个构造函数都有一个原型对象，每个原型对象都有指向构造函数的指针。而构造函数的实例对象都有一个指向原型对象的内部指针

根据最后一句，实例都有指向原型对象的内部指针。所以，可以将一个对象 Son 的原型等于另一个对象
 Parent 的实例，这样 Son 的原型就变成了 Parent的原型。  `Son.prototype = new Parent()`
```js
function Parent(name){
    this.name = name
    this.color = ['red']
}
Parent.prototype.sayName = function(){
    console.log('Parent Name: '+ this.name)
}
Parent.prototype.sayColor = function(){
    console.log('Color is '+ this.color)
}

function Son(name){
    this.name = name
}
Son.prototype = new Parent()

//  这里一定要注意 Son原型上的方法要写在Son.prototype = new Parent()后面，否则会被覆盖
Son.prototype.sayName = function(){
    console.log('Son Name: ' + this.name)
}

var instance = new Son('Helen')
instance.sayName()      //Son Name: Helen
instance.color.push('green')          
instance.sayColor()      // Color is red,green
```
这里 `instance.color.push('green') ` 会改变 Parent 原型上的属性是因为包含引用类型的原型属性会被所有实例所共享，Array是引用类型的，即多个相同的引用副本会指向同一内存空间，所以 Son 实例上的修改会影响到整个原型链 。  

为了解决原型中包含引用类型所带来的问题，引入了 `借用构造函数继承`

## 借用构造函数继承
在子类构造函数内部调用超类构造函数，例如：
```js
function Parent(){...}
function Son(){
    Parent.call(this)
}
```
此时，子类通过 Parent.call(this)将子类中变量在父类中执行了一般，即把和Parent相关的this全部绑定到this上
```js
function Parent(name){
    this.name = name
    this.color = ['red']
}
Parent.prototype.sayName = function(){
    console.log('Parent Name: '+ this.name)
}
Parent.prototype.sayColor = function(){
    console.log('Color is '+ this.color)
}

function Son(age){
    Parent.call(this,'Helen')
    this.age = age
}
// Son.prototype = new Parent()
Son.prototype.sayName = function(){
    console.log('Son Name: ' + this.name)
}
Son.prototype.sayAge = function(){
    console.log('Age is: '+ this.age)
}

var instance = new Son('Helen','21')
instance.sayName()              // Son name: Helen
instance.sayAge()                 // Age is: 21
//instance.color.push('green')
instance.sayColor()             //TypeError: instance.sayColor is not a function
```
很明显，这里子类不能继承父类原型上的方法和属性，所以提出了组合继承。通过`Son.prototype = new Parent ` 去继承父类原型上的属性和方法，通过 `Parent.call(this)` 去继承实例属性和方法

## 组合继承
```js
function Parent(name){
    this.name = name
    this.color = ['red']
}
Parent.prototype.sayName = function(){
    console.log('Parent Name: '+ this.name)
}
Parent.prototype.sayColor = function(){
    console.log('Color is '+ this.color)
}

function Son(age){
    Parent.call(this,'Helen')                    //第二次调用
    this.age = age
}
Son.prototype = new Parent()            //第一次调用
Son.prototype.sayName = function(){
    console.log('Son Name: ' + this.name)     
}
Son.prototype.sayAge = function(){
    console.log('Age is: '+ this.age)
}

var instance = new Son('Helen','21')
instance.sayName()             //Son name: Helen
instance.sayAge()                 //Age is: 21
instance.color.push('green')
instance.sayColor()              //Color is: red,green

var instance1 = new Son('javascript','20')
instance1.sayColor()            //Color is: red
```
为了继承父类原型上的属性和方法，引入`Son.prototype = new Parent()` ，但是这种方式实际上增加了一次调用构造函数，如果父类构造函数的属性和方法超多，无疑对性能的影响是非常大的。

回到问题的本身，我们要解决的问题是让子类去继承父类原型上的问题，所以构造一个空函数，让这个空函数的原型指向父类原型同样也是可以解决问题的，这里就引入寄生组合式继承
## 寄生组合式继承
```js
function Parent(name){
    this.name = name
    this.color = ['red']
}
Parent.prototype.sayName = function(){
    console.log('Parent Name: '+ this.name)
}
Parent.prototype.sayColor = function(){
    console.log('Color is '+ this.color)
}

function Son(age){
    Parent.call(this,'Helen')
    this.age = age
}
//改变这行代码Son.prototype = new Parent()
//构造一个空函数
function create(proto){
    function F(){}
    F.prototype = proto
    F.prototype.constructor = F
    return new F()
}
Son.prototype = create(Parent.prototype)
```
ES5中以及规范了这种构造空函数的方式，可以直接使用 `Object.create()` 来实现
```js
//将构造空函数替换为
function create(Parent,Son){
    Son.prototype = Object.create(Parent.prototype)
    Son.prototype.constructor = Son
}
create(Parent,Son)
```

## 资料参考
 [30 分钟学会 JS 继承](https://zhuanlan.zhihu.com/p/25578222)