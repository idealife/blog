---
title: XP系统中，客户端软件导致系统不能正常访问网络的问题记录
date: 2017-05-20 15:57:35
categories:
tags: [调试]
---
最近接到了一些用户的反馈，打开我们的客户端软件后，不仅登录不上，而且还会导致浏览器不能正常访问网络。不过这部分用户有一个共同的特征：用的XP系统。虽然XP已经落后这个时代了，但是也不能彻底放弃它，毕竟用户还在上面。

所以就开始了漫长的问题查找，首先要复现出用户的问题。在XP的虚拟中成功复现了问题，那么查原因就好办多了。

<!--more-->
发现用户登录的时候，很多登录时相关的请求都会失败，这才恍然大悟。最近更新了部分服务器，但是DNS解析还会解析到原来的IP地址，导致客户端在登录的断线重连。

```
netstat -na | find "SYS_SEND"
```
通过命令发现出现十个SYS_SEND后，后面的请求就不会再继续发送，网络被阻塞住。

### Windows XP SP2 has a limitation of 10 outgoing half-open TCP connections (i.e.: TCP connections in the "SYN_SENT" stage)

所以修复DNS的指向就能解决这个问题。

另外也是有工具可以修改XP中的最大连接数的，这个对于用户而言是有些不友好的，这是那个时代的Geek玩家的玩法。

`This is a security feature of XP (SP2 onwards), and a pretty useless one at that. You need to edit the tcpip.sys file. Windws XP SP2 introduces a few new twists to TCP/IP in order to babysit users and "reduce the threat" of worms spreading fast without control. In one such attempt, the devs seem to have limited the number of possible TCP connection attempts per second to 10 (from unlimited in SP1). This argumentative feature can possibly affect server and P2P programs that need to open many outbound connections at the same time. This site provides an automatic (and manual method if you're worried about the Event ID 4226 Patcher application) for editing the tcpip.sys file. I used the automated (application) many years ago and it worked like a charm`
