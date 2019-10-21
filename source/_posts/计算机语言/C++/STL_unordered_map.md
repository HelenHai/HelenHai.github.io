---
layout: pages
title: 【C++】STL之unordered_map
tags:
      - grammer 
categories: 计算机语言
---
## 前言 ##
今天在刷题的时候遇到关于哈希的解题方法，所在在此特别记录一下。
**<p style="color:#15A05D;display：block">(Since C++11)在STL中，哈希表对应的容器是unordered_map,替代了之前的hash_map。</p>**
>哈希表是根据关键码值 `(key,value)` 而直接进行访问的数据结构。通过把关键码值映射到表中一个文职来访问记录，以加快查找速度。

## 与map的区别 ##
**<p style="color:#15A05D;display：block">STL中，map 对应的数据结构是红黑树。（红黑树是一种近似于平衡二叉树，里面的数据是有序的）在红黑树上做查找操作的时间复杂度是O(logn)，而 unordered_map 对应哈希表。哈希表查找效率高，时间复杂度为O(1)，空间复杂度会高出很多。</p>**

## unordered_map示例 ##
```c++
#include<iostream>
#include<unordered_map>
#include<string>
using namespace std;

int main(){
      unordered_map<int,string>map;
      map.insert(make_pair(1,"张三"));
      map.insert(make_pair(2,"李四"));
      map.insert(make_pair(3,"王五"));
      map.insert(make_pair(4,"马六"));
      map.insert(make_pair(5,"七七"));

      //遍历哈希表
      unordered_map<int,string>::iterator it;
      if((it=map.find(4)!=map.end())){
            cout<<it->second<<endl;
      }
      return 0;

}
```
## unordered_map常用方法 ##

|||
|:-|:--------|
|at|返回指向unordered_map中指定key元素位置的迭代器|
|begin|返回指向unordered_map中第一个元素的forwar|
|bucket|返回指定mapkey的bucket索引|
|end|返回指向unordered_map的结尾位置之后位置的迭代器|
|find|返回指向unordered_map中指定key的元素位置的迭代器|
|insert|在unordered_map的指定位置插入一或多个元素|
|swap|交换两个unordered_map中的元素|
**更对用法请移步至：http://en.cppreference.com/w/cpp/container/unordered_map**

## 关于std::unordered_map::end ##
在官方文档中有如下定义：
>Returns an iterator to the element following the last element of the container.
This element acts as a placeholder; attempting to access it results in undefined behavior.

![](STL_unordered_map/range-begin-end.svg)
**也就是说，end在这里充当一个占位符，事实上他不指向任何元素，相当于null，不能强行访问**

**参考**
[1]http://www.howsoftworks.net/cplusplus.api/std/indexunordered_map.html
[2]http://en.cppreference.com/w/cpp/container/unordered_map

**【ps:若有不对之处还望批评指正】**