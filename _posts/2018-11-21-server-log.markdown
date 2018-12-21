---
layout: post
title: "服务器维护记录（1）"
subtitle: "禁止服务器挂起"
date: 2018-11-21
author: Xiaoming
category: Skill
tags: ubuntu suspension
finished: true
---

## Ubuntu禁止系统自动挂起

服务器设备是一台戴尔工作站，原机配送的ubuntu系统，服务器软件安装好了以后，发现运行一段时间就会自动的断网，经过分析发现是因为计算机被挂起。如何防止计算机被定期挂起？网上查了一些解决方法，有的说可以在电源选项里面关闭休眠，但实际上这个并不是休眠，而是挂起，两者应该不是一个概念。后来又修改了logind的一些配置参数，都没有生效。最后参考了一篇文章，主要修改了以下文件：/etc/polkit-1/localauthority/50-local.d/com.ubuntu.disable-suspend.pkla，修改后的内容如下：

```
[Disable suspend (upower)]
Identity=unix-user:*
Action=org.freedesktop.upower.suspend
ResultActive=no
ResultInactive=no
ResultAny=no

[Disable suspend (logind)]
Identity=unix-user:*
Action=org.freedesktop.login1.suspend
ResultActive=no
ResultInactive=no
ResultAny=no
[Disable suspend when others are logged in (logind)]
Identity=unix-user:*
Action=org.freedesktop.login1.suspend-multiple-sessions
ResultActive=no
ResultInactive=no
ResultAny=no
```

不知道具体是哪一段代码生效了，所以就都放上来了，供以后服务器维护的时候参考。服务器版本为：Linux version 4.4.0-139-generic (buildd@lcy01-amd64-006) (gcc version 5.4.0 20160609 (Ubuntu 5.4.0-6ubuntu1~16.04.10) ) #165-Ubuntu SMP Wed Oct 24 10:58:50 UTC 2018。
