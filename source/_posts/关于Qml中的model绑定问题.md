---
title: 关于Qml中的model绑定问题
date: 2016-08-26 18:12:34
categories: Qml
tags: [Qml]
---
Qml中ListView,GridView等视图组件可以通过model属性绑定数据源，通过delegate进行渲染。model属性可以有多种选择，可以是ListModel类型，也可以是数字，或者JavaScript中的数组。Repeater组件中的model更是可以接收任意类型。

`A ListView displays data from models created from built-in QML types like ListModel and XmlListModel, or custom model classes defined in C++ that inherit from QAbstractItemModel or QAbstractListModel.
A ListView has a model, which defines the data to be displayed, and a delegate, which defines how the data should be displayed. Items in a ListView are laid out horizontally or vertically. List views are inherently flickable because ListView inherits from Flickable. `
<!--more-->
如果是通过C++自定义model类型的话，需要继承QAbstractItemModel，这样子的话model的变化就会通知View进行重新渲染。否则底层数据变化，需要手动刷新界面。

`A model can be defined by subclassing QAbstractItemModel. This is the best approach if you have a more complex model that cannot be supported by the other approaches. A QAbstractItemModel can also automatically notify a QML view when the model data changes.`

下面看一个使用JavaScript中的数组对象例子，通过注释1，注释2的方式修改数组的内容，并不会触发数据对象本身的变化，也就不会进行视图的更新，需要手工重新给model指向数组对象或者让数组对象变化，才能触发更新。
```
import QtQuick 2.7
import QtQuick.Window 2.2
import QtQuick.Controls 1.4

Window {
    visible: true
    width: 640
    height: 480
    title: qsTr("Hello World")

    property var list: ['Hello','Qml','!']

    Row{
        id: row
        Repeater{
            id: repeater
            model: list
            Rectangle {
                width: 100; height: 40
                border.width: 1
                color: "yellow"
                Text{
                    id: label
                    verticalAlignment: Qt.AlignVCenter
                    horizontalAlignment: Qt.AlignHCenter
                    width: 40
                    height: 40
                    text: modelData
                    color: 'red'
                }
            }
        }
    }

    Button{
        x:0
        y:60
        width: 100
        height: 30
        text: "Update Data"
        onClicked: {
            /* 1 不会触发list对象的change
            list.push("I")
            list.push("Love")
            list.push("Qml!")
            */

            /* 2 不会触发list对象的change
            list[1] = "Qt"
            */

            // 3 对象变化
            list = ['Hello','World']
        }
    }

    Button{
        x:120
        y:60
        width: 100
        height: 30
        text: "Refresh View"
        onClicked: {
            //针对1 2的情况，需要重新绑定model 才会更新视图
            repeater.model = list;
        }
    }
}

```
