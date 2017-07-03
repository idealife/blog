---
title: Hexo站点的一些配制总结
date: 2017-06-03 15:54:24
categories: Hexo
tags: [Hexo]
---
这段时间重新收拾了一下Github Pages上的博客信息，也更新了新版本的Hexo。
为了能更友好的对待搜索引擎,针对性的做了一些优化。

1、支持Rss订阅

需要安装hexo-generator-feed插件，hexo generate的时候就会在站点根目录中生成atom.xml，Rss订阅器有了这个xml后就能获取相关的文章内容了。
```
$ npm install hexo-generator-feed
```
2、支持搜索引擎收录

安装hexo-generator-sitemap插件，hexo generate的时候会在站点根目录中生成sitemap.xml,这个xml的使用会在下面具体说说使用方式。
```
$ npm install hexo-generator-sitemap
```
<!--more-->
3、让Github Pages支持百度搜索引擎的收录

开始的时候百度是能正常收录的，后来被Github屏蔽了。

所以找了一个偷懒的办法，就是在coding.net上新建了一个镜像站(coding也提供了Pages的服务)。所以我们只要在部署的时候多部署一次就可以同步了。配置也很方便，如下：
```
deploy:
  type: git
  repo:
    github: https://github.com/idealife/idealife.github.io.git,master
    coding: https://git.coding.net/superdong/superdong.coding.me.git,master
  message: update the blog
```

这是第一步，我将coding Pages的地址放在了[百度站长](http://zhanzhang.baidu.com/)平台上管理。添加站点的时候需要验证。采用的是文件验证的方式，将下载的baidu_verify_xxx.html文件放在站点的根目录下即可。

这里会有个小插曲就是baidu_verify_xxx.html的页面内容本身只是一个验证码，但是Hexo在生成的时候会对所以页面进行模板处理。这样的话就无法通过百度的验证。需要通过配置`layout: false`屏蔽此页面的模板化。
```
layout: false
---
e2PybgZtJj
```

验证通过后，就可以在网页抓取--链接提交中，提交sitemap.xml了，百度就会周期性的抓取检查您提交的sitemap，对其中的链接进行处理。如图:
![图片](/img/201706/1-1.png)

Google的话，可以在[Google站长平台](https://www.google.com/webmasters)中，抓取-站点地图 中 主动提交sitemap.xml的url即可。

