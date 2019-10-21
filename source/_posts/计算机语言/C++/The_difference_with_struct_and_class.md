---
layout: pages
title: 【C++】结构体struct和类class区别
date: 2017-07-7 10:02:03
tags:
      - grammer 
categories: 计算机语言
---
## 前言 ##
学习数据结构时经常会遇到 `struct` 结构体，今天详细了解一下在C++中结构体和类到底有什么异同

## 摘要 ##
<!--**<p style="color:#15A05D;display：block"></p>**-->
**<p style="color:#15A05D;display：block">事实上，C++语言继承了C语言的 struct ，并对其加以扩充。在C语言中 struct 只能定义 数据成员，而不能定义成员函数。而在C++中，struct 类似于class，既可以定义成员变量，也可以定义成员函数.
然鹅...他们到底有什么区别呢？【敲黑板！！】使用class定义成员变量和函数的都是 `private` ,使用struct默认结构体定义的成员函数和变量都是 `public` 属性</p>**

**<p style="color:red;display：block">Talk is cheap, Show me the code</p>**

## 实栗一 ##

![](The_difference_with_struct_and_class/eg1.gif)

**以下为 C++ 部分代码**
```c++
//struct示例
struct book{
      double private;
      char *title;
      void display();  //成员函数
};

void book::display(){
      //Do someting
}

int main(){
      book math;             
      math.price = 50;        //OK
      math.title = "Gao Shu"; //OK
      math.display();         //OK

      return 0;
}
```
## 实栗二 ##
![](The_difference_with_struct_and_class/eg2.png)
```c++
//class示例
class book{
      double private;
      char *title;
      void display();  //成员函数
};

void book::display(){
      //Do someting
}

int main(){
      book math;             
      math.price = 50;        //ERROR 没见过这个变量
      math.title = "Gao Shu"; //ERROR 没见过哦
      math.display();         //ERROR 这什么破函数，没见过

      return 0;
}
//当然，将上述成员函数和和变量声明为 public 的话问题就迎刃而解了2333
```
**【ps:若有不对之处还望批评指正】**