---
layout: post
title: "Truecrypt在ArchLinux中无法加载加密文件的解决方法"
author: Xiaoming
category: Skill
tags: Truecrypt ArchLinux
date: 2016-05-12
finished: true
---

我在ArchLinux中使用Truecrypt的时候，出现了问题，就是不论是那个加密文件，加载的时候都会出现“Invalid charactors”的错误。网上查了一下，这个错误仅仅出现在TrueCrypt的某个版本中（应该是最新的版本），属于ArchLinux维护中出现的一个问题。解决办法有两个：一个是降低Truecrypt的版本，另一个是用命令行进行操作。

```bsh
sudo cryptsetup open --type tcrypt img.crypt mycrypt
```

这个命令里，`img.crypt`是加密文件，`mycrypt`是给这个文件起的设备名。这个命令的作用就是把加密文件作为 一个存储设备加载到Linux系统中。一般情况，到这时，系统应该自动识别到插入了新的设备，会自动`mount`。如果没有自动`mount`，可以用下面的命令：

```bsh
sudo mount /dev/mapper/mycrypt mountdir
```

这个命令的作用是手工`mount`这个设备。其中`/dev/mapper/mycrypt`是设备名，就是上一条命令实现的。

然后就可以操作里面的文件了。


