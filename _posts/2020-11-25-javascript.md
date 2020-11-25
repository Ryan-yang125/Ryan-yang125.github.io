---
layout: post
title: "浏览器中Javascript的运行机制"
subtitle: "EvenLoop究竟是如何实现的 "
date: 2020-11-25
author: "RyanYang"
header-img: "img/post-bg-default.jpg"
tags: [Javascript,Meta,浏览器]
---

> Javascript语言详解，第二篇 || 同时也是浏览器架构的第一篇

Javascript是一门*weird  language*。它在某些地方以某种方式运行着，但你就是无法完全、彻底理解它。

对于C/C++/Java这样的静态/编译型语言（强类型语言），我们清楚它是由gcc通过编译将源代码转换为二进制代码，当运行程序的时候，连接器把程序从硬盘复制到内存中并且运行。即使是python这样的动态/解释型语言（弱类型语言），也拥有自己特定的解释器把源代码转换成字节码的中间形式，然后再把它翻译成计算机使用的机器语言并运行。这一切都显得非常直观。

但Javascript最初是为了实现更为复杂的网页功能，而以一种浏览器脚本语言的形式出现，这也意味着它的运行机制都与浏览器紧密相关，因此若想彻底理解Javascript的运行机制，势必要对浏览器有一定了解，这也是本专题的目的所在。

废话不多说，直接开始正题。

首先看一段代码

```javascript
var a = 1;
var b = 2;
function add(a,b){
    return a+b
}
var c = add(a,b);
console.log(c)
```

毫无疑问，输出3，运行机制是：

- 运行前：函数声明（`function add`），变量声明(`var a;var b; var c`) 
- 运行时：a=1;b=2;c= add(a,b);console.log()`

即使加上作用域和闭包和this指针概念，会使得代码分析起来更复杂一些，但总的来说，还是符合我们对解释型语言直观的印象：从上到下，逐行解释。

可是当涉及到回调函数时