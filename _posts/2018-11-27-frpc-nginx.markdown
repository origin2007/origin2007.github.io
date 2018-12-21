---
layout: post
title: "解决Nginx反向代理不能使用非80端口的问题"
subtitle: ""
date: 2018-11-27
author: Xiaoming
category: Skill
tags: frpc nginx 反向代理 负载均衡
finished: true
---

## 问题描述

在使用nginx反向代理功能的时候，发现如果配置的Nginx端口号不是默认的80（例如81），那么在执行反向代理的时候，客户端（浏览器）会返回网站无法打开的错误，经过分析，发现在被代理的网站返回内容的时候，链接里都是没有端口号（81）的，所以导致浏览器无法继续获取网站内容，从而出现错误。

例如下述配置的网站就不可访问：

```nginx
server{
  listen 81;
  server_name example.com;
  proxy_set_header X-Forwarded-For $remote_addr;
  
  location / {
         proxy_pass         http://127.0.0.1:8000;
         proxy_set_header   Host $host;
         proxy_set_header   X-Real-IP $remote_addr;
         proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
         proxy_set_header   X-Forwarded-Host $server_name;
         proxy_read_timeout  1200s;

         # used for view/edit office file via Office Online Server
         client_max_body_size 0;

         access_log      /var/log/nginx/example.access.log;
         error_log       /var/log/nginx/example.error.log;
    }
}
```

## 解决的方法

解决的方法就是`proxy_set_header Host XXXX`这一行，在这里需要把对应的端口号放上去，例如可以改成`proxy_set_header Host example.com:$server_port`。这样在返回的所有链接里都会自动把端口号加上。

## Fprc的解决

上述方法对于外网可访问的情况下是没问题的，现在有很多网站需要通过内网穿透的方式提供服务，就要使用frpc。一般情况下frps提供的端口号和nginx的端口是不一样的，用`$server_port`就不行了。目前的解决方法就是把frps提供web服务器的端口号直接写入到nginx的配置文件里：`proxy_set_header Host example.com:7080`。这里7080就是frps提供的转发端口号。
