---
title: NSOutlineView风格设置
date: 2015-11-17 14:41:25
categories: iDev
tags: [Xcode,Xib]
---
项目中用到了NSOutlineView,所以就在苹果的官网找了相关的Demo了解其具体的用法，代码下载链接：
[SidebarDemo](https://developer.apple.com/library/mac/samplecode/SidebarDemo/Introduction/Intro.html#//apple_ref/doc/uid/DTS40010893-Intro-DontLinkElementID_2)。
熟悉了代码后，依葫芦画瓢，顺利完成了自己的代码编写，可是运行后的效果，和demo中的效果竟然完全不一样，尝试了各种方式均不能解决。
通过比对NSOutlineView的每个属性，终于在一个不起眼的位置发现了一个重要的属性：`Highlight`。这个关键真的是有很大的误导性，要是取成Style什么的也不会让我纠结那么久了。
<!--more-->

### 期望的效果

Demo中`Highlight`属性设置为Source List, 如图
![图片1](/img/201511/2_1.png)
并且代码中需要做如下处理
```
- (BOOL)outlineView:(NSOutlineView *)outlineView isGroupItem:(id)item {
    return [_topLevelItems containsObject:item]; //根节点返回的时YES;
}
- (BOOL)outlineView:(NSOutlineView *)outlineView shouldShowOutlineCellForItem:(id)item {
    // As an example, hide the "outline disclosure button" for FAVORITES. This hides the "Show/Hide" button and disables the tracking area for that row.
    return YES;
}
```
显示效果如下（鼠标放上去显示的Show和Hide会自动根据本地化设置做对应的显示，目前应该还没有自定义的方法，据说是个pravite api）
![图片2](/img/201511/2_2.png)

### 改造 验证

稍微改变一下代码
```
- (BOOL)outlineView:(NSOutlineView *)outlineView isGroupItem:(id)item {
    return NO;//[_topLevelItems containsObject:item];
}

```
如下所示，父节点的伸缩不在通过Show\Hide完成了，而是通过前面的倒三角控制了。界面也不怎么好看了。
![图片3](/img/201511/2_3.png)

再此基础上，继续修改代码
```
- (BOOL)outlineView:(NSOutlineView *)outlineView shouldShowOutlineCellForItem:(id)item {
    // As an example, hide the "outline disclosure button" for FAVORITES. This hides the "Show/Hide" button and disables the tracking area for that row.
    return NO;
}
```
现在的效果变成了下面的图，`shouldShowOutlineCellForItem`这个方法的作用是控制父节点是否能响应伸缩事件。
![图片4](/img/201511/2_4.png)

下面两图分别是`Highlight`属性设置为Regular 和 None的效果，视觉效果都很糟糕，但是不同的场景下，可能还是需要这类效果的。设置None后即使是选中列表项也不会高亮的。
![图片6](/img/201511/2_6.png)
![图片7](/img/201511/2_7.png)