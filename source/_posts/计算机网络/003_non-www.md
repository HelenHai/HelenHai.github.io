---
layout: pages
title: 为什么越来越多的网站不用www？
date: 2017-05-15 23:18:03
tags:
      - www 
      - 域名
categories: 计算机网络
---
## 摘要 ##
<!--**<p style="color:#15A05D;display：block"></p>**-->
**<p style="color:#15A05D;display：block">现在越来越多的网站域名不加 `WWW` 前缀，比如Twitter、weibo还有我自己的个人博客地址。那么加不加 `www` 到底有什么意义或者好处呢？</p>**
## 关于 WWW  ##
>WWW是环球信息网的缩写，（亦作“Web”、“WWW”、“'W3'”，英文全称为“World Wide Web”），中文名字为“万维网”，"环球网"等，常简称为Web。

**<p style="color:#DE5448;display：block">而最早加入 `WWW` 是为了区分 `FTP` 协议 </p>**

## 不加 `WWW` 的优点 ##
1. 域名更加简短，方便记忆。尤其是301 定向之后和加 `WWW` 效果更好
2. 去掉 WWW 实质上是对用户体验而做的改进

## 不加 `WWW` 的缺点 ##
在讲缺点之前先了解一下一些概念， `A记录` 和 `CNAME记录`
> - `A记录`：指定 `主机名` 或 `域名`对应的IP地址记录。用户可以将该域名下的网站服务器指向到自己的web server上。同时也可以设置您域名的二级域名。
> + CNAME记录 ：即 `别名记录` ，这种记录允许您将多个名字映射到另外一个域名。通常用于同时提供WWW和MAIL服务的计算机。 例如，有一台计算机名为“host.mydomain.com”（A记录）。同时提供WWW和MAIL服务，为了便于用户访问服务。可以为该计算机设置两个别名（CNAME）：WWW和MAIL。这两个别名的全称就 “http://www.mydomain.com/” 和“mail.mydomain.com”。 实际上他们都指向 “host.mydomain.com”。

**缺点**
> - `裸域名` 只能绑定 `A记录`，不能绑定 `CNAME记录` 。即不能把裸域设定为另外域名的别名。如果使用第三方托管服务，遇到第三方迁移服务器导致IP变更，你就必须自己去更改 `DNS` 的 `A记录`。
> + 其次，裸域名 `Cookie` 的作用范围太大，假如百度也采用裸域， 那么百度所有 Cookie 的作用范围就包括 `baidu.com` 下所有子域名。
也就是说访问 `a.baidu.com` 和 `b.baidu.com` 的时候会带上 `baidu.com` 裸域页面设置的 `Cookie`。
从隐私、安全、可扩展性、以及管理角度而言，这对很多大型网站都是不可接受的。

## 解决网址权重分散问题 ##

最佳的方式是采用 `301跳转` 并且跳转的时候保留 URL 里域名后的全部内容。举例如下：

```http
如果决定采用裸域: http://example.com,
那么{
      务必将: http://www.example.com/foo/bar?spam=egg 
     `301 跳转到`:  http://example.com/foo/bar?spam=egg 
}
反之不使用裸域{
      务必将: http://example.com/foo/bar?spam=egg 
      `301跳转到` :http://www.example.com/foo/bar?spam=egg 
}
```
**<p style="color:red;display：block">总之，使用301定向之后和有无 `WWW` 是一样的</p>**