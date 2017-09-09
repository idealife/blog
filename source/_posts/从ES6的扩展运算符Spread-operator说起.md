---
title: 从ES6的扩展运算符Spread operator说起
date: 2017-07-09 21:05:14
categories: 前端
tags: [ES6]
---
扩展运算符（spread）是三个点（...）。它好比 rest 参数的逆运算，将一个数组转为用逗号分隔的参数序列。
这个是ES6中的一个新特性，大大简化了代码。

ES6中的扩展运算符通过Babel，会转化如下的ES5代码。
<!--more-->
![](/img/201707/1-1.png)

很明显，上面的代码告诉我们扩展运算符后面的参数如果是数组的话，则通过数据遍历的方式返回新的数组。如果不是数组类型的话，则通过Array.from返回新的数组。

上面是大概的基础知识铺垫，那么下面问题就来了。

Array.from方法用于将两类对象转为真正的数组：类似数组的对象（array-like object）和可遍历（iterable）的对象（包括ES6新增的数据结构Set和Map）。这个也是ES6的新特性，浏览器的支持也不是很广泛，Babel的这种做法无疑是给我们埋了一个很大的雷，随时会爆炸。

今天这个雷被我踩上了。线上的项目有部分用户的手机页面打不开，查了发现报的异常是`undefined is not a function (evaluating 'Array.from(arr)')`。
最终定位的地方是对Uint8Array使用了扩展操作符，虽然我们觉得这个也是一个数组，但是对于Array.isArray来说，他不是标准的Array数组，返回的是False。从而进了Array.from的坑。


修改的方式有两种：
* 1、增加Array.from的polyfill；
* 2、将扩展操作符的代码通过apply的方式化解

