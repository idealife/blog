---
title: initWithFrame和initWithCoder的新发现
date: 2015-11-17 14:01:53
categories: iDev
tags: [Mac OS X, Xcode]
---
**背景**
一般的认识是这样子的：
1、当我们所写的程序里没用用Nib文件(XIB)时,用代码控制视图内容，需要调用initWithFrame去初始化
```
- (id)initWithFrame:(CGRect)frame
{
    if (self =[superinitWithFrame:frame]) {
        // 初始化代码
    }
    return self;
}
```
<!--more-->
2、用于视图加载nib文件，从nib中加载对象实例时，使用 initWithCoder初始化这些实例对象
```
- (id)initWithCoder:(NSCoder*)coder
{
    if (self =[superinitWithcoder:coder]) {
        // 初始化代码
    }
    return self;
}
```
作为一个初学者，我对上面的知识点深信不疑。
但是一个是github上的项目，彻底打乱了我的“三观”。我按照给的demo的流程创建了我的程序，结果我的项目死活不会去调用initWithFrame(该控件内部有初始化代码)，导致不能出现正常的效果。而是会去执行 initWithCoder。
如果是多一事不如少一事的心态的话，我在initWithCoder 中增加初始化代码这个事情就结束了。
不过我是不会放过这个学习的机会的。按照我的认知，我这边的执行流程才是正确的，因为用到了nib文件。但是我真的特别好奇他是怎么做到的，到底是用了什么黑科技。各种尝试探索都无解后，QQ的技术群在深夜中也被我刷的火热。
刨根问底之路又开始了。


**探索**
偶然间发现苹果官方的customview也是会去执行initWithFrame 。不科学啊。而我完全再次仿造的项目还是很固执地执行initWithCoder。
![执行对比](/img/201511/1_1.png)
[官方Demo](https://developer.apple.com/library/mac/samplecode/DragItemAround/Introduction/Intro.html#//apple_ref/doc/uid/DTS10003900)
于是简单替换了一下xib,我的项目竟然也执行了initWithFrame。看来还是xib中配置引起的。仔细对比了一遍（其实还是有点仓促的，还在忙其他的事情），毫无收获，又开始对比xib源文件（xml格式），很为是不同版本的Xcode生成的，因此差异还是很大，是个比较麻烦的工作。同时也把这个情况公布了，在几个群友的帮助一下。终于发现了打开这个大门的钥匙。

**真相**
新版本的Xcode中有个Prefer coder的选项，新工程的xib都是默认选中的，对应的xml文件中会有customObjectInstantitationMethod=”direct”的属性值。这个属性决定了到底是执行initWithFrame还是initWithCoder。
![结果1](/img/201511/1_2.png)
![结果2](/img/201511/1_3.jpg)
