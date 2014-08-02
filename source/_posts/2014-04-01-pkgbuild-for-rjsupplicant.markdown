---
layout: post
title: "Linux 版锐捷客户端的 PKGBUILD"
date: 2014-04-01 19:58
comments: true
categories: [Linux, Arch Linux, Network]
---

在愚人节的今天，学校全面强制使用 4.99 版本的锐捷客户端作为上网认证，之前一直在 Arch 下默默辛勤工作的 [MentoHUST][mht] 就这样挂掉了。。。   

锐捷其实也有 Linux 版的，叫做 rjsupplicant，试了一下居然能用，不用离开 Linux 回到 Windows 算是万幸，不过不出所料的无法开多网卡建 WiFi 热点了，也只好如此。   

自从学了一下下 PKGBUILD 怎么写，无论多小的程序或脚本，不用 `Pacman` 管理就不舒服，所以这次也打了个包，文件都在[这里][download]。   

安装好后输入 `rjsupplicant --help` 查看帮助，我的认证格式如下：
```
sudo rjsupplicant -a1 -d0 -nenp2s0 -u<用户名> -p<密码>
```

- -a:   1 的话表示有线连接，0 就是无线
- -d:   1 使用 dhcp，0 不使用
- -n:   网卡名

退出可以按 `q`。   

打包里还有个 `Systemd` 的 service 文件，从 `AUR` 上的 [mentohust-bin][aur] 里修改而来。安装好后如果要启用，先打开 `/usr/lib/systemd/system/rjsupplicant.service`，在[第 10 行][service]添加上上述的认证参数，想在 `makepkg` 前添加的话请记得更新 `PKGBUILD` 里对应的 `md5sum` 值。



[mht]: https://code.google.com/p/mentohust/
[download]: https://github.com/wenLiangcan/PKGBUILD/tree/master/rjsupplicant
[aur]: https://aur.archlinux.org/packages/mentohust-bin/
[service]: https://github.com/wenLiangcan/PKGBUILD/blob/master/rjsupplicant/rjsupplicant.service#L10

