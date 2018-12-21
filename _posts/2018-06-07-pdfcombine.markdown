---
layout: post
title: "Linux下合并pdf的方法"
subtitle: "使用命令行"
date: 2018-06-07
author: Xiaoming
category: Skill
tags: Linux pdf 
finished: true
---

## 使用gs命令

```
gs -dNOPAUSE -sDEVICE=pdfwrite -sOUTPUTFILE=firstANDsecond.pdf -dBATCH first.pdf second.pdf

```


## 另外一种方法：Boomaga

Boomaga是一种虚拟打印机，可以将各种打印任务合并到一起打印，也可以设置每张纸打印多页，很方便。

```
sudo add-apt-repository ppa:boomaga
sudo apt-get update
sudo apt-get install boomaga
```

打印的时候选择打印机boomaga，然后会自动打开boomaga的窗口，你可以编辑，合并和打印，还可以导出PDF文件。

