---
layout: post
title: "解决 Linux 下 KeePass 中文显示的问题"
date: 2014-04-20 23:05
comments: true
categories: [Linux]
---

Linux 下用 KeePass 中文一直以来都显示为方块，准确的说是点开编辑条目的时候，外面的列表显示倒没有问题，弄得我一直不敢在 Linux 下编辑我的数据文件，每次想要添加新帐号都得开个虚拟机。。。   

突然发现原来是 Local 设置的问题，因为我习惯使用英文作为系统语言，所以只要让 KeePass 使用中文 Local 运行就好了。比如 Arch 下安装的官方源里的包的话，在 `/usr/bin/keepass` 里加入：   
```bash
export LANG=zh_CN.utf8
```
