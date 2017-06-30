---
title: Grunt配置的相关备忘
date: 2016-02-03 12:29:58
categories: 前端
tags: [Grunt]
---
路径规则：

path/to/js/**/*.js

匹配 path/to/js/ 及其子目录下所有的js文件，**匹配path/to/js/的0个或多个子文件夹。

css合并：

需要插件grunt-contrib-cssmin和grunt-contrib-cssminconcat,通过以下配置进行合并
```
        useminPrepare: {
             html: 'index.html',
            options: {
                dest: 'temp/'
            }
        }
```
<!--more-->
效果是将index.html中
```
    <!-- build:css css/app.css -->
    <link type="text/css" media="all" rel="stylesheet" href="/common/css/base.css" />
    <link type="text/css" media="all" rel="stylesheet" href="css/jquery-ui.min.css" />
    <!-- endbuild -->
```
生成对应的合并后css，如图：

![图片](/img/201602/1-1.png)

有build标记的文件替换
上述代码合并为 
```
  <link rel="stylesheet" href="css/app.css" media="all”>
```
则需要通过usemin(需要安装grunt-usemin插件)的配置进行
```
        usemin: {
            html: ['index.html'],
            options: {
                blockReplacements: {
                    'css': function(block) {
                        var media = block.media ? ' media="' + block.media + '"' : '';
                        return '<link rel="stylesheet" href="css/' + block.dest + '"' + media + '/>';
                    },
                    'js': function(block) {
                        var defer = block.defer ? 'defer ' : '',
                            async = block.async ? 'async ' : '';
                        return '<script ' + defer + async + 'src="css/' + block.dest + '"><\/script>';
                    }
                }
            }
        }
```
