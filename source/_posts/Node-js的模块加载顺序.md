---
title: Node.js的模块加载顺序
date: 2016-01-19 22:54:23
categories: 前端
tags: [NodeJs]
---
Node.js的模块的载入及缓存机制如下：
1）载入内置模块（A Core Module）
2）载入文件模块（A File Module）
3）载入文件目录模块（A Folder Module）
4）载入node_modules里的模块
5）自动缓存已载入模块。

如果模块名不是路径，也不是内置模块，Node将试图去当前目录的node_modules文件夹里搜索。如果当前目录的node_modules里没有找到，Node会从父目录的node_modules里搜索，这样递归下去直到根目录。
可以在项目目录下使用 node 命令（已在路径`C:\Users\xxx>`为例），然后输入 global.module.paths查看Node.js加载node_modules模块的顺序。
<!--more-->
![路径](/img/201601/1-1.png)

这里重点说明一下require载入文件模块和文件目录模块的相关机制：

1、优先载入文件模块，一般使用的是相对路径（也可以用绝对路径），如：
```
var myMod = require('./my_mod')  //js的后缀可以省略
```
2、当对应文件没找到的时候，开始找文件夹。Node将搜索整个my_mod目录，Node会假设my_mod为一个包并试图找到包定义文件package.json。如果my_mod目录里没有包含package.json文件，Node会假设默认主文件为index.js，即会加载index.js。如果index.js也不存在，那么加载将失败。如果有package.json，则会找package.json中`main`属性对应的值，如：`"main": "mod.js`

#### 额外再讲点npm--save --save-dev的区别
-save和save-dev可以省掉你手动修改package.json文件的步骤。

npm install module-name -save 自动把模块和版本号添加到dependencies部分，

npm install module-name -save-dev 自动把模块和版本号添加到devdependencies部分。

至于配置文件区分这俩部分， 是用于区别开发依赖模块和产品依赖模块，再做单个module的时候可能体验不到两者的差异。

但当前模块A变成其他模块的依赖，在其他模块的主目录中执行npm install 时，只会下载A的dependencies下的依赖。如果此时进入到这个A模块的主目录中，执行npm install时，才会把devdependencies的依赖下载下来，方便你对A模块进行调试相关的工作。
