---
title: Qt5.7 Qml的项目部署问题
date: 2017-01-20 20:39:45
categories: Qml
tags: [Qt,Qml]
---
Qt的跨平台是个好东西，但是平台的差异巨大，想要一招鲜吃天下还是有点难度的，因为Qt的Qml跨平台之路也不是那么的顺风顺水，还有很多坑要求去填。

今天就说说打包的问题。
<!--more-->
在Windows中使用Qt的安装包中的工具windeployqt.exe。
由于QtCreator在Windows中的Debug速度很慢，基本都在Release中进行开发，故Release的配置中不再配置deploy相关的任务，将部署单独使用脚本处理:   
> `C:\Qt\Qt5.7.0\5.7\msvc2013\bin\windeployqt.exe -qmldir C:\Qt\Qt5.7.0\5.7\msvc2013\qml XXX.exe`  

由于Qt的部署工具还未完善，除此之外，上述命令处理后，除了需要Deployment中需要的文件之外，还需要手动从`C:\Qt\Qt5.7.0\5.7\msvc2013\qml`将`QtWebEngine`、`QtQuick`、`QtGraphicalEffects`这三个文件复制到部署目录中（完全覆盖即可，这些组件是项目中用到的）。

更多内容详见官方文档[windows-deployment](http://doc.qt.io/qt-5/windows-deployment.html)。 

注：

1、程序运行发现Qt5.7中窗口的变化有明显的黑影现象，和底层的图形库有关，因此libEGL.dll`、`libGLESV2.dll`、`libGLESV2.dll`这三个dll使用的是Qt5.6中带的库。

2、还需要增加msvcp120.dll`、`msvcp120.dll`这两个Windows系统中所需的VC++的运行库

在macOS中的部署类似，Debug的速度不慢，调试在Debug模式下，发布在Release模式。因此就将macdeployqt直接配置在Release中。首先在QtCreator的项目Release版本的设置中增加自定义构建步骤，如图所示：
![图片](/img/201701/1-1.png)。

具体方法详见Qt官方文档[osx-deployment](http://doc.qt.io/qt-5/osx-deployment.html)。

同时也需要手工复制源码下的`QtGraphicalEffects`、`QtWebEngine`至`XXX.app/Contents/Resources/qml`中。