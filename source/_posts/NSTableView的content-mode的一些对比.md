---
title: NSTableView的content mode的一些对比
date: 2015-12-01 11:11:28
categories: iDev
tags: 
---
content mode 分为cell-based和view-based两种模式，他们之间有什么区别呢？

先上[Demo](https://developer.apple.com/library/mac/samplecode/TableViewPlayground/Introduction/Intro.html)

WWDC上的相关文档[view_based_tableview](http://adcdownload.apple.com/wwdc_2011/adc_on_itunes__wwdc11_sessions__pdf/120_view_based_tableview.pdf),[TableViewOverview](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/TableView/TableViewOverview/TableViewOverview.html)

关于这两者的区别，cell-based是更早期的做法，简单高效，但是随着技术的发展，需要更多更炫的效果，就诞生了view-based.苹果是建议使用view-based模式的。下面是官方的说法：

<!--more-->
In addition to following description I would suggest you to go throughhttp://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/TableView/Introduction/Introduction.html

In OS X version 10.6 and earlier each individual cell within a table view was required to be a subclass of NSCell. This approach has caused limitations when designing complex custom cells, typically requiring the developer to write their own NSCell subclass. Additionally, providing animation, such as progress views, was extremely difficult.

In OS X version 10.7 table views have been redesigned and now support using views as individual cells. These are referred to as view-based table views. View-based table views allow you to design custom cells in the Interface Builder portion of Xcode 4.0. It allows easy design time layout as well as making it easy to animate changes and customize drawing. As with cell-based table views, view-based table views support selection, column dragging, and other user-expected table view behavior. The only difference is that the developer is given much more flexibility in design and implementation.

Creating view-based and cell-based table views and adding columns use the same techniques within Interface Builder. The differences occur in your application code when providing the individual cells, populating the content of the table view, and customizing the table view appearance. As well, the Cocoa bindings techniques are entirely different between the two implementations.

A cell can contain only one UI element like a text cell, image view cell, button cell and a few more. The customization ability is quite poor.

A view can contain multiple UI elements as well as other views. The customization ability is almost infinite.

Apple recommends to use always view based table views

另外cell-based可以有列选中的效果，view-based则没这个效果。
![cell-based](/img/201512/1-1.png)
![view-based](/img/201512/1-2.png)

代码中要改变conntent mode,则需要
改变table的datasouredelegate才行了。

嗯，一个delegate专门负责cell相关的委托实现 一个专门负责view的委托实现。
Cell-based对应的是 tableView:objectValueForTableColumn:row:，而view-based NSTableView 对应的则是 tableView:viewForTableColumn:row:.
感觉苹果这个设计的有点繁琐，界面中有设置的地方，纯代码竟然要通过这种方式制定。可能是为了结构更加独立，减少相互之间的干扰。