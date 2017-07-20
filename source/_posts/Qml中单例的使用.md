---
title: Qml中单例的使用
date: 2016-06-09 21:33:32
categories: Qml
tags: [Qml]
---
1、Qml中的单例实现
* 编写组件
```
//ThemeManager.qml
pragma Singleton  //单例标志
import QtQuick 2.0  
  
Item {  
    property string name: "singleton"  
}
```  
* 建立连接

    相同目录下，新建qmldir文件,

    在文件中添加`singleton ThemeManager          0.1 ThemeManager.qml`

* 使用
其他qml中，只要import ThemeManager.qml所在的文件夹路径就能直接使用ThemeManager 这个单例了。
<!--more-->
2、Qml文件使用C++的提供的单例
* 创建继承自QObject的C++类，datachannel
* main.cpp中定义对应的变量和方法，并进行注册
```
DataChannel * channel = NULL;

static QObject *datachannel_singletontype_provider(QQmlEngine *engine, QJSEngine *scriptEngine)
{
    Q_UNUSED(engine)
    Q_UNUSED(scriptEngine)

    return channel;
}

 qmlRegisterSingletonType<DataChannel>("Demo.Channel", 1, 0, "Channel", datachannel_singletontype_provider);

```
* Qml中使用，导入对应的模块后，就能直接调用了。
```
// 导入C++中注册的连接模块
import Demo.Channel 1.0;
Item {
    id: channel;
        property int state: Channel.getState();
}
```

3、Qml中使用JS文件

    只需要在js文件中，添加`.pragma library`的标识，然后在Qml中通过别名的方式引用。如下：
    `import "../js/Util.js" as Util`

