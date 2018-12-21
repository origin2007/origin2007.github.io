---
layout: post
title: "Eclipse RCP 中的FileEditorInput不见了"
subtitle: "Eclipse RCP的坑之一"
date: 2018-12-17
author: Xiaoming
category: Develope
tags: Eclipse RCP Plugin
finished: true
---

## Eclipse 4.8.0 中的FileEditorInput找不到了？

进行日常RCP的开发，发现惯用的FileEditorInput类居然找不到了，原来是新版本的eclipse将其放到了org.eclipse.ui.ide\_XXXX.jar这个文件里了。这是eclipse日常的坑之一，经常在版本升级的时候将一些常用的类移来移去，分特！

