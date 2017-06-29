---
title: Xcode程序本地化设置
date: 2015-11-18 14:37:44
categories: iDev
tags: [Xcode]
---
对于Xcode中实现程序的本地化花了点时间做了简单地了解。
首先Xcode中实现本地化有两种机制：

#### 一种是Use Base Internationalization

#### 另一种则是不用

前者是后来苹果改进后的做法，所以现在创建的默认程序都是用的Use Base Internationalization的方法，官网中老的demo则都是第二种方式。两者相结合使用，则会更灵活地实现需要的效果。
两则的区别是Use Base Internationalization后，会创建Base.lproj的文件夹，然后把相关的xib文件都放到里面。而翻译的内容则会放到对应语言的lproj文件夹中，如英语，则在en.lproj文件夹中又对应xib的strings文件，只要在这里面做对应的翻译即可。
而第二种方式，更直接，但是会更占空间，对应语言的文件夹(如en.lproj)中将会多出一份在Base.lproj文件夹中的xib的copy，所有的翻译都在xib上进行即可。（也可以重新调整界面）
<!--more-->

### 设置方式

在工程的info中，Localizations节点下的Use Base Internationalization即可选择本地化的方式。要实现更多语言的本地化，则在Language中添加即可。如下图。添加后，在xib的File inspector中的Localization中就多出刚增加的语言。也能定制当前xib的本地方方式。如图二，Localizable Stirngs可以单独设置为Interface Builder Cocoa XIB,就被配置成了第二种本地化方式。
![图一](/img/201511/4_1.png)
图二：
![图二](/img/201511/4_2.png)
