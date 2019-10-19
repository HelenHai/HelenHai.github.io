---
title:  美化hexo博客（一）—— 段落背景突出美化
date: 2017-05-29 11:22:28
tags: 
      - hexo
categories: 博客搭建
---

## 摘要 ##
**<p style="color:#15A05D;display：block">查询Markdown文档后我并没有发现如何让段落更加突出，或者说如何给某个段落添加背景色。
而next主题本身自带的 [高亮] 并不是很突出。所以目前比较好得一个解决方案是通过添加HTML代码来实现 [高亮突出显示]</p>**

## 实现方式 ##
```html
<table>
  <tr>
    <td bgcolor="FAEBD7">这里是段落文字。。。</td>
  </tr>
</table>
```

**网页展示效果**

<table><tr><td bgcolor="FAEBD7">
  这里是段落文字....
  这里是段落文字....
  
</td></tr></table>