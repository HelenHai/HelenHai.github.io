---
layout: pages
title: JavaScirpt DOM 编程实例学习（一）
date: 2017-05-17 19:31:03
tags:
      - javascirpt 
      - DOM
      - Node节点
categories: javascript
---
## 摘要 ##
<!--**<p style="color:#15A05D;display：block"></p>**-->
**<p style="color:#15A05D;display：block">DOM（文档对象模型）是针对 HTML，XML文档的一个 `API` 。所以 DOM 本质上是为基本的文档结构及查询提供了接口，这个接口不仅仅可以由 `javascirpt` 调用，也可用通过 `python` 调用来实现网页的重构</p>**

## Node节点层次 ##
Node节点一共有有12个，而我们 `必须记住的` 或者经常使用的有 `5个`。

| 类型          | nodeType| nodeName | nodeValue
|:-------|:---:|:------| :-----:|
| 元素 ELEMENT_NODE   |  1  | 元素标记名（DIV/P/...） |null
| 属性 ATTRIBUTE_NODE |  2  | 属性名（name/value/...）|属性值
| 文本 TEXT_NODE      |  3  |                         |文本内容
| 注释 COMMENT_NODE   |  8  |                |注释的内容
| 文档 DOCUMENT_NODE  |  9  |                     |null