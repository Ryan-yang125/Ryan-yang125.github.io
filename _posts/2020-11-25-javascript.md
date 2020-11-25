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

## 简单的Javascript

Javascript是一门*weird  language*。它在某些地方以某种方式运行着，但你就是无法完全、彻底理解它。

对于C/C++/Java这样的静态/编译型语言（强类型语言），我们清楚它是由gcc通过编译将源代码转换为二进制代码，当运行程序的时候，连接器把程序从硬盘复制到内存中并且运行。即使是python这样的动态/解释型语言（弱类型语言），也拥有自己特定的解释器把源代码转换成字节码的中间形式，然后再把它翻译成计算机使用的机器语言并运行。这一切都显得非常直观。

但Javascript最初是为了实现更为复杂的网页功能，而以一种浏览器脚本语言的形式出现，这也意味着它的运行机制都与浏览器紧密相关，因此若想彻底理解Javascript的运行机制，势必要对浏览器有一定了解，这也是本专题的目的所在。

> 以下均以Chrome为标准

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
- 运行时：`a=1;b=2;c= add(a,b);console.log()`

即使加上作用域和闭包和this指针概念，会使得代码分析起来更复杂一些，但总的来说，还是符合我们对解释型语言直观的印象：从上到下，逐行解释。

## 异步回调的Javascript

可是当涉及到异步回调函数时，这一切就变得很诡异了

```javascript
var a = 0;
setTimeout(()=>{a++},0)
console.log(a);
```

控制台会输出什么, 0 or 1?事实上它会输出0，但当我们查看a的值时已经变成1了。

![截屏2020-11-25 15.03.40](https://gitee.com/Ryan-yang125/picture-bed/raw/master/upic/%E6%88%AA%E5%B1%8F2020-11-25%2015.03.407XlFiS.png)

很难接受，但我们明白了：即使`setTimeout`为0，它也会在“当前代码”执行完毕之后再去执行其中的回调函数。

但Javascript不是以单线程的形式运行的吗，它是如何做到一边等（或者发出Http请求），一边运行之后的代码呢？

## V8 和 Chrome

这里要理清一个关系：在Chorme中，Javascript的*runtime environment*是V8，这是一个以单线程的形式执行Js代码的解释器，它大概长这样：

![截屏2020-11-25 15.14.29](https://gitee.com/Ryan-yang125/picture-bed/raw/master/upic/%E6%88%AA%E5%B1%8F2020-11-25%2015.14.29rJ5viq.png)

heap用来分配对象，并把引用（地址）保存在栈中，stack用来分配基础数据类型（按值访问）和调用函数，关于内存分配之后再写一篇文章专门分析，这里着重关注函数调用。

而在V8 engine中，是没有`setTimeout(),DOM,Http` 这些函数的，换句话说，这些都是浏览器提供给我们的api，并不是在V8中运行的！

因此，一个更为完整的浏览器运行Javascript的框架实际长这样：

![截屏2020-11-25 15.22.30](https://gitee.com/Ryan-yang125/picture-bed/raw/master/upic/%E6%88%AA%E5%B1%8F2020-11-25%2015.22.30ZcQ2NZ.png)

这里WebAPIs是一个统称，实际上可能包括网络进程（ajax）,渲染进程（DOM），callback queue也分为多个:macro queue, micro queue, repaint queue。

在Javascript运行时，Eventloop是按下面的逻辑进行的：

![eventloop1](https://gitee.com/Ryan-yang125/picture-bed/raw/master/upic/eventloop1J4pHfS.png)

在解析HTML时，每当遇到一个`<script>`标签，V8添加一个

