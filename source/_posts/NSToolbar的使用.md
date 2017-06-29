---
title: NSToolbar的使用
date: 2015-11-18 15:03:17
categories: iDev
tags: [Xcode,Xib]
---
1、[NSToolbar的官网demo地址](https://developer.apple.com/library/mac/samplecode/ToolbarSample/Introduction/Intro.html#//apple_ref/doc/uid/DTS10000413)
2、[官方PDF资料](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/Toolbars/Toolbars.pdf)

<!--more-->
特别要注意的是，由于上面的两份资料年代久远，有一些功能在新的OSX SDK已经被弃用了，如Toolbar Separator 和Customize items。
![图片](/img/201511/3_1.png)

下面讲解已打印功能讲一下NSToolbar使用中要注意的地方
首先要实现NSToolbarDelegate中的方法，是Item可用
```
//--------------------------------------------------------------------------------------------------
// We don't do anything useful here (and we don't really have to implement this method) but you could
// if you wanted to. If, however, you want to have validation checks on your standard NSToolbarItems
// (with images) and have inactive ones grayed out, then this is the method for you.
// It isn't called for custom NSToolbarItems (with custom views); you'd have to override -validate:
// (see NSToolbarItem.h for a discussion) to get it to do so if you wanted it to.
// If you don't implement this method, the NSToolbarItems are enabled by default.
//--------------------------------------------------------------------------------------------------
-(BOOL)validateToolbarItem:(NSToolbarItem *)toolbarItem
{
    return YES;
}
```
因为NSToolbarPrintItem是系统已经定义好了的，所以需要在toolbarWillAddItem自定义我们需要的实现。对于PrintItem而言如果不设置setTarget或者setAction的话，它是回一直处在不可用的状态的。不设置Target，则validateToolbarItem好像是不会进入的（我目前的程序中是这样的，但是demo中，把下面的代码注释掉，PrintItem也一直是可用的。这部分还需要探索一下）。
```
//--------------------------------------------------------------------------------------------------
// This is an optional delegate method, called when a new item is about to be added to the toolbar.
// This is a good spot to set up initial state information for toolbar items, particularly ones
// that you don't directly control yourself (like with NSToolbarPrintItemIdentifier here).
// The notification's object is the toolbar, and the @"item" key in the userInfo is the toolbar item
// being added.

//--------------------------------------------------------------------------------------------------
- (void)toolbarWillAddItem:(NSNotification *)notif
{
    NSToolbarItem *addedItem = [[notif userInfo] objectForKey:@"item"];
    
    // Is this the printing toolbar item?  If so, then we want to redirect it's action to ourselves
    // so we can handle the printing properly; hence, we give it a new target.
    //
    if ([[addedItem itemIdentifier] isEqual: NSToolbarPrintItemIdentifier])
    {
        //[addedItem setToolTip:@"Print your document"];
        [addedItem setTarget:self];
    }
}
```
实现打印方法.也可以通过上面setAction重新定义打印的具体实现代码。
```
//--------------------------------------------------------------------------------------------------
// The NSToolbarPrintItem NSToolbarItem will sent the -printDocument: message to its target.
// Since we wired its target to be ourselves in -toolbarWillAddItem:, we get called here when
// the user tries to print by clicking the toolbar item.
//--------------------------------------------------------------------------------------------------
- (void)printDocument:(id)sender
{
    NSPrintOperation *printOperation = [NSPrintOperation printOperationWithView: [self.mainContentView window].contentView];
    [printOperation runOperationModalForWindow: [self.mainContentView window] delegate:nil didRunSelector:nil contextInfo:nil];
}
```
最终效果
![图片](/img/201511/3_2.png)
