---
layout: pages
title: toLowerCase和toLocaleLowerCase的区别
date: 2018-08-17 14:05:03
tags:
      - javascript
categories: 计算机语言
---
ECMAScript中涉及字符串大小写转换的方法有4个：toLowerCase()、toLocaleLowerCase()、toUpperCase()和toLocaleUpperCase()。

对有些地区来说，针对地区的方法与其通用方法得到的结果相同，但少数语言(如土耳其语言)会为Unicode大小写转换应用特殊的规则，这时候就必须使用针对地区的方法来保证实现正确的转换。

```js
var stringValue = "hello world";
alert(stringValue.toLocaleUpperCase());    //"HELLO WORLD"
alert(stringValue.toUpperCase());          //"HELLO WORLD"
alert(stringValue.toLocaleLowerCase());    //"hello world"
alert(stringValue.toLowerCase());          //"hello world"123456
```