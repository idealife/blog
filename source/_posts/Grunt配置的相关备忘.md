---
title: Grunt配置的相关备忘
date: 2016-02-03 12:29:58
categories: 前端
tags: [Grunt]
---
#### 备忘一:
路径规则：

path/to/js/**/*.js

匹配 path/to/js/ 及其子目录下所有的js文件，**匹配path/to/js/的0个或多个子文件夹。

### 备忘二:
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
### 备忘三:
使用grunt时我们会用到各类插件,有时候一个复杂的项目打包需要很长时间,这个时候我们可以去找一些方法优化,通过插件`time-grunt`,我们可以了解到每一个步骤的耗时,从而可以改进流程,优化打包速度.
```
// Gruntfile.js
module.exports = grunt => {
	// require it at the top and pass in the grunt instance
	require('time-grunt')(grunt);

	grunt.initConfig();
}
```

### 备忘四:
平时在grunt.js中使用插件，都需要如下所示，使用loadNpmTasks预先加载相关的插件，显得特别繁琐和不必要。
```
    grunt.loadNpmTasks('grunt-usemin');
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-cssmin');
```
使用`load-grunt-tasks`插件就能减少上述不必要的代码,package.json文件中的dependencies/devDependencies/peerDependencies 字段内指定的插件都会被加载。
```
// Gruntfile.js 
module.exports = grunt => {
    // load all grunt tasks matching the ['grunt-*', '@*/grunt-*'] patterns 
    require('load-grunt-tasks')(grunt);
 
    grunt.initConfig({});
    grunt.registerTask('default', []);
};
```