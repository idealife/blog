---
title: 一张图看懂JavaScript正则
date: 2016-03-16 15:47:28
categories: 前端
tags: [JavaScript]
---
网上看到的图片，千言万语都在其中。
不多说了，直接上图

<!--more-->
![正则整理](/img/201603/1-1.png)

备忘：
```
var str = "name is {{name}}, age is {{age}}";

var reg = /{{[a-zA-Z_][a-zA-Z0-9\.]*}}/g;
console.log(str.match(reg));

var reg2 = /{{[a-zA-Z_][a-zA-Z0-9\.]+}}/g;
console.log(str.match(reg2));

var reg3 = /{{([a-zA-Z_][a-zA-Z0-9\.]+)}}/g;//注意这里的小括号位置，用于replace中的分组
console.log(str.match(reg3));

str.replace(reg3, function(raw, key, offset, string){
  console.log(1111, raw, key, offset);
});

var reg4 = /{{([a-zA-Z_])([a-zA-Z0-9\.]+)}}/g;//注意这里的小括号于上述位置不同，用于replace中的分组
str.replace(reg4, function(raw, key,key2, offset, string){
  console.log(2222, raw, key, key2, offset);
});
```