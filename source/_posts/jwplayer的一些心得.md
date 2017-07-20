---
title: jwplayer的一些心得
date: 2016-04-05 20:05:35
categories: 前端
tags: [jwplayer]
---

过年的时候阿里微信的红包大战打的火热，产品也准备蹭个热点，搞了一个活动页，仿阿里的咻一咻。

声音播放用的是jwplayer,有点杀鸡用牛刀的感觉，没办法，特殊原因必须需要支持Chrome/25.0.1364.172，`<audio>`如此方便的标签只能看不能用，让我先哭会去。
<!--more-->
jwplayer用的是6.8版本，最新的7.3版本在官方注册后就能无水印使用了，6.8的话，需要自己DIY一下，将logo.png指向到本地加载。

6.8以后的版本除了水印问题，另外还会去请求p.jwpcdn.com下面的jwpsrv.js文件，而这个域名在国外，可能会受到干扰，从而导致打开速度变慢。

修改方式：
```
        q.repo = function() {
            // var t = "http://p.jwpcdn.com/" + g.version.split(/\W/).splice(0, 2).join("/") + "/";
            // try {
            //     if (q.isHTTPS()) {
            //         t = t.replace("http://", "https://ssl.")
            //     }
            // } catch (u) {
            // }
            var t = "./";  //修改为本地获取相关资源
            return t
        };
```

在使用中也要注意,即使将播放器的长宽设置为0,在播放时,播放的控制按钮还是会出现的,所以可以将播放器所在的元素移到不可见的区域做隐藏处理
```
        //初始化播放器
        jwplayer("player").setup({
            file: "voice.mp3",
            width: 0,
            height: 0
        });
```

如果是Windows平台，jwplayer在低版本的浏览器上还是需要flash对应版本的支持的，如果没有安装flash或者flash版本过低，播放将会失败，就需要使用其他的方式规避这个问题了。
```
/**
 * 检查flash版本
 */
function flashChecker() {
    var hasFlash = 0;　　　　 //是否安装了flash
    var flashVersion = 0;　　 //flash版本

    if (document.all) {
        var swf = new ActiveXObject('ShockwaveFlash.ShockwaveFlash');
        if (swf) {
            hasFlash = 1;
            VSwf = swf.GetVariable("$version");
            flashVersion = parseInt(VSwf.split(" ")[1].split(",")[0]);
        }
    } else {
        if (navigator.plugins && navigator.plugins.length > 0) {
            var swf2 = navigator.plugins["Shockwave Flash"];
            if (swf2) {
                hasFlash = 1;
                var words = swf2.description.split(" ");
                for (var i = 0; i < words.length; ++i) {
                    if (isNaN(parseInt(words[i]))) continue;
                    flashVersion = parseInt(words[i]);
                }
            }
        }
    }
    return {
        f: hasFlash,
        v: flashVersion
    };
}
```
