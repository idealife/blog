---
title: Hexo部署时提示Fatal： Could not read from remote repository的问题处理
date: 2015-10-02 01:41:26
categories: Hexo
tags: [Git,Hexo]
---
第一次在Mac中配置好了hexo,发布的时候却一直提示：
`Error: Permission denied (publickey). fatal: Could not read from remote repository.`
分析这个错误提示大致可以推断出是公钥配置的问题引起的。

但是github中我已经配置好了当前的公钥信息了，再次检查公钥。
0、github中公钥重新配置了一遍
1、git clone可以正确执行。
2、ssh -T idealife@github.com
也提示：Hi defnngj You’ve successfully authenticated, but GitHub does not provide shell access.
说明配置的公钥没有问题。
就是在执行sudo hexo deploy的时候报公钥问题。
<!--more-->
为什么要加sudo呢？
在执行hexo命令时，如果不提权，就会一直提示db.json文件的写权限的错误：
`Unhandled rejection Error: EACCES: permission denied, open '/Users/idealife/Developer/blog/db.json' at Error (native)`
所以进行了权限提升。
以前一直是在window环境中配置，对于权限的认识比较浅。突然意识到，就是sudo引起的问题。

因为我生成公钥的命令是ssh-keygen -t rsa -C “idealife@github.com”
没有加sudo，生成的公钥是当前用户的，路径是 /Users/idealife/.ssh。而sudo hexo deploy命令执行的时候应该会去读取的root用户的公钥，很显然root下还没有对应的公钥信息生成。

随即立即验证这个推测。
1、为root生成publickey:sudo ssh-keygen -t rsa -C “idealife@github.com”，对应路径为/var/root/.ssh
2、提取公钥信息并配置到github中。问题又来了，在UI界面中是无法访问到/var下的root文件夹的。我们可以通过sudo cat /var/root/.ssh/id_rsa.pub可以绕开这个问题。
3、执行sudo hexo deploy，部署成功！

总结：在Linux或者UNIX环境中一定要注意权限问题！