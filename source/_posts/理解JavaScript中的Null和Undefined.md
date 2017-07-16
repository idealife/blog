---
title: 理解JavaScript中的Null和Undefined
date: 2016-03-26 12:07:53
categories: 前端
tags: [JavaScript]
---
当我们进行 null == undefined 的判断时，发现返回的结果是true。
因为JS标准规范定义:
>If x is null and y is undefined, return true。

所以可以通过 var a;  if (a == null){} 判断对象是否为空。 
<!--more-->
关于undefined:

1、如果变量未做任何定义，就直接使用的话，则会报错。
if (b){}; //代码报错`Uncaught ReferenceError: b is not defined at <anonymous>:1:1`

或者只声明了参数，没有进行赋值。访问其不存在的属性时则会出现这样的报错：`Uncaught TypeError: Cannot read property 'name' of undefined at <anonymous>:1:21`。因为在赋值之前，定义的变量会被初始为undefined。

2、定义了对象，但是使用的属性还没有定义，
如`var c ={}; console.log(c.name)`. 输出为undefined。

JS中的5钟基本类型

可以通过Object的toString()方法获取。
1、Object.prototype.toString.call(null);
"[object Null]"
2、Object.prototype.toString.call(undefined);
"[object Undefined]"
3、Object.prototype.toString.call(true);
"[object Boolean]"
4、Object.prototype.toString.call(10086);
"[object Number]"
5、Object.prototype.toString.call('Hello JavaScript');
"[object String]"

如果是对象的话，则如下：

var obj ={}; obj.toString()
"[object Object]"

另外typeof方法也可以获取对应的类型
![图片](/img/201603/2-1.png)