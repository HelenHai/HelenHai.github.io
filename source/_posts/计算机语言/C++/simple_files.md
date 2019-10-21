---
layout: pages
title: C++ 文件输入输出
date: 2017-05-24 10:01:03
tags:
      - grammer 
categories: 计算机语言
---
## 摘要 ##
<!--**<p style="color:#15A05D;display：block"></p>**-->
**<p style="color:#15A05D;display：block"></p>**

## 文件输入 ##
### 一、 简单的向一个文件写入数据 ###
```c++
#include <iostream>
#include <fstream>

//为了向一个文件写入数据，首先需要创建一个ofstream类型对象
ofstream output;

//指定写入文件，需要调用output对象的open函数
//此语句会创建一个名为number的txt文件。如果文件存在，其内容会被销毁，并创建一个新的文件
output.open("number.txt");

//向文件写入数据与发送数据到cout对象一样，使用流插入运算符
output << 95 << " " << 36 << " " << 22 << " " << 1;

//关闭文件
output.close();

```

## 文件输出 ##
### 一、简单的从一个文件读取数据 ###
```c++
#include <iostream>
#include <fstream>

//读取文件，首先声明一个ifstream类型变量
ifstream input;

input.open("number.txt");

int score;
int total = 0;
	
//调用input对象里的eof()函数来检测文件尾
while (!input.eof()){
	input >> score;
	cout << "number.txt中的数据为： " <<score << endl;
	total += score;
}
```
>文件读取完后，应调用input的close函数关闭文件
当文件中已经没有内容可读取的时候，eof()函数会返回true。这个信息是从操作系统获取的