---
layout: post
title: "Nexus 7 升级 4.4 KitKat 后重新提权"
date: 2013-11-27 14:33
comments: true
categories: [Android, Linux]
---

今天我的 N7 二代终于收到 OTA 了，升级后又要重新获取 Root 权限，之前这些工作一直用 Windows 下的 [Nexus Root Toolkit][nrt] 完成，现在尝试在 Linux 下解决。   

之所以升级后 Root 权限没了，是因为 su 文件被移除了，所以现在只需要重刷一次 [SuperSU][su] 的升级包。刷升级包需要第三方 Recovery，之前 Root 机子时并没有把它直接刷进机器里，而是采用临时启动的方式，现在也用这个方法。   

- 先要确保电脑上有 adb 和 fastboot， Arch 下可以安装 aur 上的 [android-sdk-platform-tools][aur] 包。
- 然后下载 SuperSU 的[升级包][zip] 和 [TWRP][twrp]。
- 通过 USB 数据线连接设备到电脑。
- 把 SuperSU 升级包直接复制到 N7 上，选一个方便找到的路径。
- 打开终端，cd 到 Recovery 镜像所在的目录。
- 使用 adb 重启到 BootLoader：   
```bash
$ adb reboot-bootloader
```   

- 出现 BootLoader 介面后先看看设备能否被识别：
```bash
$ fastboot devices
```   

- 没问题的话就可以启动镜像了：
```bash
$ fastboot boot ./openrecovery-twrp-2.6.3.1-flo.img 
```   

- 成功进入 TWRP 后点 Install，选择 SuperSU 升级包，刷之。返回主介面，Reboot -> System，OK !   

ps :   
之前之所以不刷入 Recovery，是因为那时的 TWRP 还不支持 N7 二代的分辨率，整个介面缩在了一边，所以其实是刷过一次觉得很难看又删掉了 ╮(￣▽￣")╭ 。这次倒是发现已经支持了，不过不想刷了，觉得没必要，想要尽量保持机子的纯净。   

刚刚才知道，原来 SuperSU 设置里有一个 叫 Survival mode 的功能，就是用来防止系统 OTA 更新后权限丢失的。不过不能保证百分百有效而且是付费版才能用，我倒是装了个网上找的 key，这次把它勾上，期待下次更新，看有没有效 o((≧▽≦o)


##参考资料
一个[刷机包][cmd]里的批处理文件，来自[a860官方和临时Recovery互刷工具][blog]



[nrt]: http://www.wugfresh.com/nrt/‎
[su]: http://forum.xda-developers.com/showthread.php?t=1538053
[aur]: https://aur.archlinux.org/packages/android-sdk-platform-tools/
[zip]: http://download.chainfire.eu/supersu
[twrp]: http://techerrata.com/browse/twrp2/flo
[cmd]: http://kuai.xunlei.com/d/rMTXArBp8JmbUQQA5c0
[blog]: http://blog.sina.com.cn/s/blog_0058222c0101cddg.html