---
title: 对xib设计界面中File's Owner以及Objects的理解
date: 2015-10-10 04:36:38
categories: iDev
tags: [Xcode,Xib]
---
File’s Owner以及Objects在设计界面中的设置虽然大大地方便了程序开发，可以用更少的代码，实现复杂的功能。
但是对接触不久的开发者来说，会增加很多的迷惑性，学习成本相对高了。另一方面对于这些的理解和使用很多资料中也只是一笔带过。

**File’s Owner**
File’s Owner只是设计时的占位符，只有程序运行时，谁加载了此xib文件，谁才是其Owner。
可以在加载的代码initWithWindowNibName:Owenr:中指定其owner，也就成了运行时的File’s Owner。
在设计界面中File’s Owner的Custom Class可以随意指定，但指定的Class不符合outlet连接规则时，会有如下图的提示，
<!--more-->
![图片](/img/201510/1_1.png)
因为在运行中的owner就会完全遵循设计时的Outlets中的连接规则去执行(必须保持一致，否则调用方法时会出现找不到方法）。
因此，设计时就要指定好加载该xib的class（或者有相同的接口规范的class，这就大大提高了程序的灵活性）。

**Objects**
Objects中的添加的NSObject（也可以是NSViewContrller），在xib文件加载的时候都会被实例化。可以这么理解，等效于xib中拖放的控件，可以分2种用途，1、不显示，用于处理与界面之间的交互。2、在代码中控制界面的显示。
这些Object都可以通过outlet连接到File’s owner中，这样子File’s owner就可以统一管理界面中的元素了。Object之间也可以相互之间连接。
![图片](/img/201510/1_2.png)