---
layout: pages
title: 利用CSS 解决绝对定位遮挡层的问题
date: 2018-03-19 18:05:03
tags:
      - css
categories: 前端
---

在前端开发中会遇到的这样一类场景：

交互切的 png 过大

图片素材是个边框且面积很大

最要命的是这些素材的 z-index 都是最高，这种情况下点击事件无法穿透绝对定位浮层（hin尴尬...）

CSS 有一个属性正好可以来解决这个问题： 
```css
pointer-events: none;
```