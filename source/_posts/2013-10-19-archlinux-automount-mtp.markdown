---
layout: post
title: "Arch Linux 下自动挂载 MTP"
date: 2013-10-19 20:01
comments: true
categories: [Linux, Arch Linux, Android]
---

买了台 N7 二代，发现不支持 USB 大容量存储模式连接电脑（听说 Android 3 开始就不再支持 USB Mass Storage 协议了，不过 Defy 上刷的 CM 10 倒是可以支持，现在看来应该是第三方添加的功能了），只能以 [MTP][mtpwiki] 或 [PTP][ptpwiki] 的方式连接，导致 Arch 下一直无法自动挂载只能用 Airdroid 传文件，可是用脚本生成的 Wi-Fi 会时不时的断线，传大文件时就杯具了 QAQ       

终于让我找到了这个 [PKGBUILD 包][pkgbuild]，[下载][download]下来后需要先修改一下 ``PKGBUILD`` 和 ``99-android-mtp.rules`` 这两个文件。      

###一、
首先是为你使用的设备添加相应的挂载规则。（默认的挂载规则已经加入了对 Nexus 7 一代，Galaxy Nexus 和 HP Touchpad 32GB 的支持，如果你使用的是这些设备可以跳过这一步。）     

通过 USB 线把设备连接到电脑，在终端里输入命令：
```bash
$ udevadm monitor --environment --udev
```
然后拔出设备，就可以看到类似下面的信息：
```
monitor will print the received events for:
UDEV - the event which udev sends out after rule processing

UDEV  [39055.590819] remove   /devices/pci0000:00/0000:00:12.2/usb1/1-1/1-1:1.0 (usb)
ACTION=remove
DEVPATH=/devices/pci0000:00/0000:00:12.2/usb1/1-1/1-1:1.0
DEVTYPE=usb_interface
ID_MODEL_FROM_DATABASE=Nexus 4 (debug)
ID_VENDOR_FROM_DATABASE=Google Inc.
INTERFACE=255/255/0
MODALIAS=usb:v18D1p4EE2d0228dc00dsc00dp00icFFiscFFip00in00
PRODUCT=18d1/4ee2/228
SEQNUM=1985
SUBSYSTEM=usb
TYPE=0/0/0
USEC_INITIALIZED=662109

UDEV  [39055.592153] remove   /devices/pci0000:00/0000:00:12.2/usb1/1-1/1-1:1.1 (usb)
ACTION=remove
DEVPATH=/devices/pci0000:00/0000:00:12.2/usb1/1-1/1-1:1.1
DEVTYPE=usb_interface
ID_MODEL_FROM_DATABASE=Nexus 4 (debug)
ID_VENDOR_FROM_DATABASE=Google Inc.
INTERFACE=255/66/1
MODALIAS=usb:v18D1p4EE2d0228dc00dsc00dp00icFFisc42ip01in01
PRODUCT=18d1/4ee2/228
SEQNUM=1986
SUBSYSTEM=usb
TYPE=0/0/0
USEC_INITIALIZED=48662167

UDEV  [39055.661296] remove   /devices/virtual/bdi/0:41 (bdi)
ACTION=remove
DEVPATH=/devices/virtual/bdi/0:41
SEQNUM=1988
SUBSYSTEM=bdi
USEC_INITIALIZED=660985

UDEV  [39055.667918] remove   /devices/pci0000:00/0000:00:12.2/usb1/1-1 (usb)
ACTION=remove
BUSNUM=001
DEVLINKS=/dev/libmtp-1-1
DEVNAME=/dev/bus/usb/001/016
DEVNUM=016
DEVPATH=/devices/pci0000:00/0000:00:12.2/usb1/1-1
DEVTYPE=usb_device
ID_BUS=usb
ID_FOR_SEAT=usb-pci-0000_00_12_2-usb-0_1
ID_MEDIA_PLAYER=1
ID_MODEL=Nexus_7
ID_MODEL_ENC=Nexus\x207
ID_MODEL_FROM_DATABASE=Nexus 4 (debug)
ID_MODEL_ID=4ee2
ID_MTP_DEVICE=1
ID_PATH=pci-0000:00:12.2-usb-0:1
ID_PATH_TAG=pci-0000_00_12_2-usb-0_1
ID_REVISION=0228
ID_SERIAL=asus_Nexus_7_06e9d04d
ID_SERIAL_SHORT=06e9d04d
ID_USB_INTERFACES=:ffff00:ff4201:
ID_VENDOR=asus
ID_VENDOR_ENC=asus
ID_VENDOR_FROM_DATABASE=Google Inc.
ID_VENDOR_ID=18d1
MAJOR=189
MINOR=15
PRODUCT=18d1/4ee2/228
SEQNUM=1987
SUBSYSTEM=usb
TAGS=:seat:uaccess:
TYPE=0/0/0
USEC_INITIALIZED=48644741
```
我们需要的是 ``ID_MODEL_ID`` 和 ``ID_VENDOR_ID`` 这两项等于号后面的字符串。   

接下来编辑 ``99-android-mtp.rules`` 文件，仿照已有的规则和上面得到的信息添加相应的条目，比如我的是 Nexus 7 二代：
```
# Google Nexus 7 (Wi-Fi, 2013 version) 32GB MTP mode
SUBSYSTEM=="usb", ATTR{idVendor}=="18d1", ATTR{idProduct}=="4ee1", MODE="0666" # MTP media
SUBSYSTEM=="usb", ATTR{idVendor}=="18d1", ATTR{idProduct}=="4ee2", MODE="0666" # MTP media with USB debug on

###################################
# Android devices, when unplugged #
###################################

# Nexus 7 2013
ENV{ID_MODEL_ID}=="4ee1", ENV{ID_VENDOR_ID}=="18d1", ACTION=="remove", RUN+="/usr/bin/systemctl stop 'android-mtp@$env{ID_MODEL}.service'"
ENV{ID_MODEL_ID}=="4ee2", ENV{ID_VENDOR_ID}=="18d1", ACTION=="remove", RUN+="/usr/bin/systemctl stop 'android-mtp@$env{ID_MODEL}.service'"

####################################
# Android devices, when plugged-in #
####################################

# Nexus 7 2013
ENV{ID_MODEL_ID}=="4ee1", ENV{ID_VENDOR_ID}=="18d1", ACTION=="add", RUN+="/usr/bin/systemctl start 'android-mtp@$env{ID_MODEL}.service'"
ENV{ID_MODEL_ID}=="4ee2", ENV{ID_VENDOR_ID}=="18d1", ACTION=="add", RUN+="/usr/bin/systemctl start 'android-mtp@$env{ID_MODEL}.service'"
```
这里包含了纯 MTP 模式 和 MTP，USB debug 模式同时打开时的规则，可以在手机切换到对应的模式通过上一步骤得到相关数据。    
其中，第一部分里的 ``ATTR{idVendor}`` 对应 ``ID_VENDOR_ID``，``ATTR{idProduct}`` 对应 ``ID_MODEL_ID``。     

``ID_MODEL_ID`` 除了这两种情况，在 PTP 模式，PTP 和 USB debug 模式，Bootloader 模式 还有 Recovery 模式都会有对应的数值，不过我尝试添加了 PTP 模式的规则后没能成功挂载，只好作罢。    

###二、
然后需要修改一下 ``PKGBUILD`` 中对应 ``99-android-mtp.rules`` 文件的 ``md5`` 值，无论你有没有进行上一步的修改，这个值都是错误的。               

从 AUR 安装所需的依赖 [``go-mtpfs-git``][aur1] 和 [``daemonize``][aur2]
```bash
$ yaourt -S go-mtpfs-git daemonize
```
打包
```bash
$ cd android-automount/
$ makepkg
```
安装
```bash
$ sudo pacman -U android-automount-0.1-1-any.pkg.tar.xz
```

OK，现在连接你的 Android 设备到电脑上，看看 Arch 是不是成功挂载了呢~      
如果需要断开连接，只需直接拔掉数据线就好，无需手动 unmount。     

##参考资料
- [Automounting MTP devices][post]



[mtpwiki]: http://en.wikipedia.org/wiki/Media_Transfer_Protocol
[ptpwiki]: http://en.wikipedia.org/wiki/Picture_Transfer_Protocol
[pkgbuild]: https://github.com/fxthomas/android-automount
[download]: https://github.com/fxthomas/android-automount/archive/master.zip
[aur1]: https://aur.archlinux.org/packages/go-mtpfs-git/
[aur2]: https://aur.archlinux.org/packages/daemonize/
[post]: https://bbs.archlinux.org/viewtopic.php?id=154123
