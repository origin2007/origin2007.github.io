---
layout: post
title: ArchLinux 使用过程相关问题集锦
author: Xiaoming
date: 2016-05-23
category: Skill
tags: ArchLinux SoftwareEngineering OS
finished: false
---

## Eclipse运行不正常，界面无法正确显示

安装好Eclipse后运行时会发现Eclipse的界面显示比较混乱，在运行新建工程向导的时候界面无法显示，在设置项目属性的时候点击左侧属性树结果右侧面板内容显示无更新，而且会出现空指针等错误。判断应该是SWT库跟xfce4桌面系统冲突，后来参考了[wiki上的资料](https://wiki.archlinux.org/index.php/Eclipse#Disable_GTK.2B_3)，觉得应该是gtk3的问题，因此按照上面的设置，把gtk3禁用掉。结果就运行正常了。

## RTL8723be无线网卡信号弱，经常断网

我另外一台惠普的电脑是用的RTL8723BE无线网卡，这个网卡在Linux界名声极差，使用时基本上会连上一会就断，而且信号极差，基本上离路由5米以上就无网。找了好久，在[wiki上的说明](https://wiki.archlinux.org/index.php/Wireless_network_configuration#rtl8723ae.2Frtl8723be)只能解决经常掉线的问题，信号差是无法解决的。后来知道是目前ArchLinux的内核还不支持`ant=2`的参数设置，所以只能重新编译网卡的驱动。

> ```
> sudo pacman -S linux-headers
> git clone https://github.com/lwfinger/rtlwifi_new.git
> cd rtlwifi_new
> make
> make install
> modprobe -rv rtl8723be
> modprobe rtl8723be ant=2
> ```

上述操作就是重新编译rtl网卡的内核，然后重新加载内核并使用参数`ant=2`。这个时候就会发现wifi信号蹭蹭的涨上来了。如果不想每次手动操作，就把该参数加到`/etc/modprobe.d/rtl8723be.conf`里。

PS 官方控制信号强度的参数应该是`ant_sel=2`. 我的配置文件的内容如下：

> ```
> options rtl8723be debug=1
> options rtl8723be disable_watchdog=N
> options rtl8723be fwlps=N
> options rtl8723be ips=Y msi=N swenc=N swlps=N ant_sel=2
> ```

## Pacman加速

pacman加速的方法来自于[官方wiki](https://wiki.archlinux.org/index.php/Pacman/Tips_and_tricks#Performance)。思路就是用其他的下载软件来代替Pacman自己的下载工具。这篇文章里推荐的是[aria2](https://wiki.archlinux.org/index.php/Aria2#pacman_XferCommand)。具体方法是编辑`/etc/pacman.conf`，将`[options]`部分的配置改为如下：

```
XferCommand = /usr/bin/aria2c --allow-overwrite=true --continue=true --file-allocation=none --log-level=error --max-tries=2 --max-connection-per-server=2 --max-file-not-found=5 --min-split-size=5M --no-conf --remote-time=true --summary-interval=60 --timeout=5 --dir=/ --out %o %u
```

## 网络配置与管理

ArchLinux网络配置和管理方面比较麻烦。如果您一开始就装的比方说KDE或者Gnome这种重量级的界面的话，会自动安装图形化的网络管理工具，配置起来还比较省事，否则就需要了解很多底层的命令才行。

ArchLinux包括了很多管理网络的工具，我用过的就是netctl和NetworkManager。推荐用NetworkNanager，这个比较智能。netctl相对来说比较麻烦一些，另外还有一些公有的工具，例如`ip`,`iw`, `iwconfig`等。熟练掌握此类工具可在困难的时候开启网络。

### NetworkManager

NetworkManager包括的命令有：`nmcli`, `nmtui`等。NetworkManager有桌面通知程序`networkmanager-applet`。

### Netctl

Netctl包括的命令有: `netctl`, `netctl-auto`，`wifi-menu`等。

Netctl没有图形化的通知界面，NetworkManager有，可以在图形界面上显示wifi信号并且配置网络等。

查看无线信号的强度可以使用`iwconfig`查看，这个命令获得的当前网络信息实际上是来自于`/proc/net/wireless`这个文件。

### Netctl-auto@服务无法启动

通常是netctl.profile出了问题，语法不正确。因为无线验证密码的那个程序对profile的格式要求比较严格，即使有一个profile写的格式不正确，服务也无法启动。所以比较简单的方法是把`/etc/netctl.d/`下的profile全删掉，重新利用`wifi-menu`进行连接，创建新的profile。

## 更新系统

直接使用`yaourt -Syu --aur`。

## windows zip 压缩文件解压缩的乱码问题

原因在于window所使用的汉字编码问题，解决方法是使用unzip的`-O`选项。但是ArchLinux本身的unzip是没有这个选项的，必须安装`unzip-iconv`。

> ```
yaourt unzip-iconv
unzip -O cp936 *.zip
```

该软件包会取代原先的unzip软件包。

## 声卡声音巨小

安装好ALSA驱动后虽然有声音了，但是声音很小，即使调到最大音量也不大，环境稍微吵一点就听不清楚了。网上搜了很久，应该是驱动对网卡的支持不好造成的。如果不想重新编译驱动，那么可以用我的这个方法试试。

首先确保安装`alsa`, `alsa-oss`, `pulseaudio-alsa`和`pavucontrol`。然后用`pavucontrol`来调节系统的最大音量。这样做可以提高音量，不过总感觉音质不是很好。

## ArchLinux更新时出现unkown trust怎么办

如果ArchLinux使用pacman在更新时出现了“User  is unknown trust, installation failed“这样的错误，多半是签名的key出现了问题。我也碰到了一次这样的问题。这其实不是问题，绝大多数正常使用的系统应该是需要升级archlinux-keyring了。

> pacman -S archlinux-keyring

如果不是这个问题，就更新一下签名：

> pacman-key --refresh-keys

## 多个显示器的操作

如果系统里有多个显示器，一般情况下可以用设置里的display进行设置。其实更简单的有命令行可以直接操控多个显示器的显示。这个命令就是`xrandr`。

直接输入`xrandr`会列出系统中的所有的显示设备。

比方说我的笔记本自带显示器为LVDS，通过VGA接口接了一个液晶显示器，叫VGA1.

那么就可以用这个操作将新接入的VGA1旋转90度放置在笔记本的左侧：

```
xrandr --output VGA1 --rotate left --auto --left-of LVDS
```

如果使用HDMI接了一个显示器，那么笔记本的显示器会被命名为eDP1（不要问我为什么，我也不知道），外接的显示器为HDMI1。然后命令就变为：

```
xrandr --output HDMI1 --rotate left --auto --left-of eDP1
```

Note: `--auto`意思是使用默认的分辨率。

## WPS无法输入中文

用编辑器打开"/usr/bin/wps"程序，在文件开头添加如下几行代码：

```
export XMODIFIERS="@im=fcitx"
export QT_IM_MODULE="fcitx"
```

然后重启WPS，就可以输入中文了。

## 清理系统已经安装的无用的包

系统运行久了，经常安装删除软件，会导致系统里出现一些无用的包，原来依赖这些包的应用已经被卸载了，但是包留了下来，作为使用人员如我，肯定觉得不爽，有一条命令，可以将其清理掉。

```
yaourt -Qdt
```


