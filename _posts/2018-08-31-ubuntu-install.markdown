---
layout: post
title: "安装Ubuntu后的一些操作"
subtitle: "Ubuntu"
date: 2018-08-31
author: Xiaoming
category: Skill
tags: Linux Ubuntu
finished: true
---

## 安装ShadowSocks

```
sudo apt install shadowsocks
```

然后运行sslocal连接到shadow socks服务器上。

## 使用需要翻墙的源

进行Apt安装和更新软件的时候，有些源往往被墙了，这时候需要中apt中使用代理。

```
sudo apt-get -o Acquire::socks::proxy="socks5://127.0.0.1:7070" install syncthing
```

## tmux中使用鼠标

在`/etc/tmux.conf`中加上一句`set-option -g mouse on`。


