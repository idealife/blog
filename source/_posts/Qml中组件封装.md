---
title: Qml中的组件封装
date: 2016-07-08 12:50:37
categories: Qml
tags: [Qml]
---
Qml毕竟也是这几年才刚起来的，Qt Quick Control 1.x中的Qml组件不丰富，而且封装的功能相对简单，很多效果都需要自己重新实现。不过还是入了这个坑，毕竟[Qt Quick Control 2.0](http://blog.qt.io/blog/2016/06/10/qt-quick-controls-2-0-a-new-beginning/)刚出现不久，架构和底层变了，也是各种坑需要等着填。

<!--more-->

在Qt Quick Control 1.x中，默认的样式并不能满足我们的视觉效果，所以需要重新定义Style,一般的组件都有[组件名+Style]的style样式可以修改使用。

先说一下Menu，MenuStyle的设置在Windows中没有问题，但是在MacOS中，设置的样式还是会丢失的，这个估计是Qt还没有做好底层的兼容。
另外Menu还有几个私有属性和方法未在文档中公开出来（可以在安装目录中找到对应组件的Qml源代码），但是我们在封装我们自定义的Menu组件中是需要用到的，一个是`__minimumWidth`，还有一个是`__popup()`方法。

再谈谈WebEngineView组件，对于Url的监控需要在onLoadingChanged中处理。如果要监控自定义协议类型的话，需继承QQuickWebEngineProfile实现新的Profile类，然后注册自定义协议名称(使用installUrlSchemeHandler可以注册多个协议)，就可以在Qml中的WebEngineView的profile中使用。如果网页中有QQ聊天工具的调用的话，捕获tencent://协议后，再调用外部浏览器直接将QQ唤起。

ScrollView组件要有更多的交互的话，需要用到几个私有属性：`__wheelAreaScrollSpeed` 滚动一次偏移的距离设置，`__scroller`显示的滚动条，可以通过下面的代码设置横竖滚动条的步进参数
```
__scroller.verticalScrollBar.singleStep
__scroller.horizontalScrollBar.singleStep
```


