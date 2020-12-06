---
layout: post
title: "JavaScript编译原理"
subtitle: "JS引擎是如何运作的"
date: 2020-12-06
author: "RyanYang"
header-img: "img/post-bg-default.jpg"
tags: [JavaScript,V8,编译原理,词法,XMind]
---

### 引擎是如何运作的

这是我目前为止对JavaScript编译原理（不考虑优化）的大致理解:

![V8引擎](https://i.loli.net/2020/12/06/OyDR3ZLQtwNmgSE.png)

### 词法作用域

这其中我们着重关注词法作用域的规则，包括var/let的区别，提升，闭包等概念

![变量声明与作用域](https://i.loli.net/2020/12/06/CE5buLBczAhFp3n.png)

至此，关于JavaScript词法部分的内容基本告一段落。