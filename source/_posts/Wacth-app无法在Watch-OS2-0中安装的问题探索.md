---
title: Wacth app无法在watchOS2.0中安装的问题探索
date: 2015-10-01 13:41:26
categories: iDev
tags: [Apple Watch,Xcode,watchOS2.0]
---
### 问题描述：

这段时间随着iOS9.0,以及watchOS2.0升级正式开启后，陆陆续续的就发现了很多问题。
其中一个情况就是，同事反映说，Apple Watch系统更新以后，testflight上的App无法安装watch app了，线上版本的App就能正常装上watch。
所以怀疑是苹果的证书验证机制可能变化了。毕竟Xcode7.0可以免$99真机调试了。
<!--more-->

### 探索

正好在做adhoc的签名包，也遇到了这个问题。

##### 组合1

Xcode7.0 + iOS9.0 + watchOS2.0 + adhoc证书，直接编译App到真机中，结果安装Watch App就一直无法成功，在即将安装成功时，自动卸载，也无任何错误提示。

##### 组合2

Xcode7.0 + iOS9.0 + watchOS1.0 + adhoc证书，就能很顺利的安装上了。
看来watchOS2的证书机制变化了。

##### 探索3

因为线上版本没有问题，所以就准备用adhoc证书Archive的包，通过iTunes安装，这时Watch App又能顺利装上了watchOS2.0的系统了。
此时再用组合1的方式直接编译到手机，然后安装Watch App，竟然也成功安装上了。
可能是3的操作让Watch系统上存了授权信息，从而可以绕开了。

### 相关

查了一下相关资料，发现在watchOS2.0 beta阶段有过类似的情况：
[Can anyone run watchOS2 beta 3 apps on the watch device while debugging with Xcode?](https://forums.developer.apple.com/thread/9607)
其中提到：
Update: I can run (not debug) every time on the watch by installing an ad hoc distribution through iTunes.
1、Product -> Archive [You may have some code signing issues to figure out here–especially if you have multiple people who sign the builds for your organization.]
2、Export… -> for Ad Hoc Distribution
3、Uninstall the app from your iPhone.
4、Drop the ipa file onto iTunes.
5、Select your iPhone in iTunes, go to the Apps tab, and find the app you just dropped in.
6、Click install.
7、Click apply.
8、Make sure that “Show App on Apple Watch” is selected in the Apple Watch app, and wait for it to install.
It should run at least. I wasn’t abble to attach the debugger to the processes, but I was able to see the performance of the changes that I had already tested on the simulator.
The good news is that performance of button and table responsiveness is probably 10x that of beta 2 if you have lots of buttons. It was tough to get it to run, but I feel like there was a treasure at the end of the hunt.

### 总结

因为苹果系统的封闭性，无法探索出更加有具体的原因。可能马上就会有官方的说明出来！
如果你又更好地解答，欢迎留言！
