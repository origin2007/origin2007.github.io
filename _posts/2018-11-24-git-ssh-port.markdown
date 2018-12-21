---
layout: post
title: "Git服务器SSH端口非默认时时客户端的配置"
subtitle: "Git如何指定服务器的端口号"
date: 2018-11-21
author: Xiaoming
category: Skill
tags: Git
finished: true
---

## Git如何指定推送服务器的端口号

有的Git服务器其SSH服务的端口号不是默认的22，这样在按照通常的方式进行代码推送的时候就会出现被拒绝的情况。解决方法就是配置Git客户端能够指定该服务器的SSH端口号。有两种方法：

（1）可以在命令行中加入端口号。例如原来的服务器为“git@example.com/user/repo”, 可以改成”ssh://git@example.com:7777/user/repo“，其中的7777就是服务器的SSH端口号。

（2）在~/.ssh/目录中创建config文件，里面写上端口信息：

```
Host example.com
  HostName example.com
  Port 7777
```


