---
layout: post
title: "配置Mysql让外网（外机）可访问"
author: Xiaoming
Date: 2017-02-13
category: Research
tags: mysql sofware-engineering
finished: true
---

## 问题描述

`Mysql`安装完成后，默认是`localhost`访问，即只有本机的客户端才能访问数据库，其他机器访问的时候会出现被拒绝访问的提示。因此需要配置`Mysql`使其允许来自其他IP的连接。

## 解决方法

首先先在安装`Mysql`的服务器上运行客户端：

> mysql -uroot -p

然后输入`root`的密码登录进去。然后再执行`SQL`命令：

```
use mysql;
grant all privileges on *.* to 'root'@'%' indetified by 'yourpassword'with grant option;
flush privileges
```

这样`mysql`就开启了来自外部计算机的连接。

有时候这个操作之后连接数据库还是会被拒绝，那是因为`mysql`的默认监听地址是“127.0.0.1”，需要修改一下`/etc/mysql/my.cnf`中的`bind-address`，改成“0.0.0.0”就行了。
最后别忘了重启mysql服务。

> /etc/init.d/mysql restart