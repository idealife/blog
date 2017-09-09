---
title: VS Code中的插件以及相关配置
date: 2017-06-29 20:29:02
categories: 前端
tags: [VS Code]
---
VS Code相对其他编辑器，配置相对简单，而且插件也非常丰富，总之是越来越好用了，而且越用越顺手。

团队开发当然需要统一代码的规范，不然后续的代码比对会让人疯的。
这里要主推两个插件：EditorConfig for VS Code 和 ESLint

<!--more-->
EditorConfig for VS Code
----
EditorConfig 插件会从文件所在目录开始逐级向上查找 .editorconfig，直到到达根目录或者找到包含属性 root=true 的 .editorconfig 文件为止。当找到所有满足条件的 .editorconfig 配置文件后，插件会读取这些配置文件的内容，距离文件路径最短的配置文件中的属性优先级最高，同一个文件按照从上到下方式读取，底部定义的同名属性优先级要高于顶部定义的。大部分编辑器都有这个插件，即使团队成员使用不同的IDE，也可以很好的统一代码风格。只要保证.editorconfig这个文件一直即可。

相关的配置方式可以见[官网](http://editorconfig.org/)<br/>
```
root = true
[*]
charset = utf-8
indent_style = space
indent_size = 2
end_of_line = lf
insert_final_newline = true
trim_trailing_whitespace = true
```
### 有坑注意:

上述代码配置的是tab为2个空格的缩进。当时当你格式化代码或者按tab还是会出现4个空格的情况，说明还有其他插件也在执行，导致Editorconfig无效。比如`Javascript Standard Format`等格式化插件，有了Editorconfig后，可以和这类控件暂时告别了。

ESLint
----
ESLint是一个用来识别 ECMAScript 并且按照规则给出报告的代码检测工具，使用它可以避免低级错误和统一代码的风格.配置的方式有多种，一般这些文件的优先级则是按照以上出现的顺序（.eslintrc.js > .eslintrc.yaml > .eslintrc.yml > .eslintrc.json > .eslintrc > package.json）。

ESLint的配置方式也是特别丰富，具体教程见[官网](https://eslint.org/),目前形成了Airbnb和Standard等相关配置方案，直接选择对应的规则引用使用即可，当然也能在此基础上进行自定义。

### 有坑注意:
虽然我们配置好了ESLint，写代码的时候一些不规范的位置都会有错误提示，但是我们格式化代码的配置也需要和ESLint的配置对应，比如下面的情况，代码格式化时会自动删函数名后的空格。
![](/img/201706/2-1.png)
这个时候需要查看VS Code的其他插件的行为，在首选项配置中查`insertSpaceBeforeFunctionParenthesis` 就能找到是哪个插件在暗中帮我们删的空格。
![](/img/201706/2-1.png)

其他问题
----
* 进行保存时的代码自动格式化。
如果发现配置了VS Code首选项中的两个参数， "editor.formatOnType": false,和"editor.formatOnSave": false 重启，发现不管用，依然会自动格式化。那么就极有可能是`JS-CS-HTML Formatter`这个插件捣的鬼。

* vetur插件提示 'v-for' directives require 'v-bind:key' directives
原因是vue在升级到2.2后，当在组件中使用 v-for 时， key 现在是必须的。
vetur插件的作者给出了解决办法是：
`This is intended ESLint feature. You can turn off eslint check in future release. 
Setting vetur.validation.vue-html to false will disable it.`