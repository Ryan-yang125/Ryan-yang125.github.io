---
layout: post
title: "JavaScript重点方法:Call,Apply,Bind"
subtitle: "偏函数和函数Currying初探"
date: 2021-01-15
author: "RyanYang"
header-img: "img/post-bg-default.jpg"
tags: [JavaScript]
---

**Call:**
1.F.pro.call(thisarg,arg1,arg2,…)，用于指定this然后调用函数
2.通过Parent.call(this,arg1,arg2..)实现父类属性和方法的继承
3.(function(){}).call(thisarg)IIFE调用
**Apply:**
1.F.pro.apply(thisarg,[argsArray])
2.任何具有length方法的类数组对象
3.因此和spread(…)用处类似: a.push.apply(a,b);
Math.max.apply(null,a);
4.但要注意有函数最大参数限制65536
**Bind():**
1.取出对象方法: let foo = obj1.bar.bind(obj1,args);
2.实现偏函数(partial function)：let foo = bar.bind(null, x); x被预置为bar的第一个参数形参。(主要作用)
3.setTimeout中指定this
4.new会忽略传递的this

**Currying:**

1. f(1,2,3) => f(1)(2)(3) => f(1,2)(3)
2. Currying 的作用在于生成根据函数参数切分偏函数,使得不必重复传递相同参数，以及使得函数语义更加明确
CC Pool:
1.使用Promise + queue控制每一时刻异步执行的任务数量，即并发控制池

