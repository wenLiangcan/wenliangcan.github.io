---
layout: post
title: "IPv6 到 IPv4 的代理"
date: 2014-05-18 01:30
comments: true
categories: [Network, Arch Linux]
---

为了安装 [Pandoc][pandoc] 添加了 [haskell-core][repo] 这个仓库（`AUR` 里的包要自己编译太麻烦了），不过要用 Goagent 代理才能访问，本来以为是墙了。   

可是问题来了，更新/安装一般的官方仓库的包时我是不希望通过代理的，所以会先注释掉 `pacman.conf` 里的 `haskell-core` 再 `pacman -Syu`，这样一来已安装的 `haskell` 包会引起依赖问题导致更新无法继续。   

无意中想起用 [http://cloudmonitor.ca.com/en/ping.php][justping]（以前叫 just-ping.com）ping 一下这个仓库的地址（xsounds.org）看看，结果发现这货只支持通过 `IPv6` 访问！！！而我学校的网络只支持 `IPv4`。。。   

Google 了一番后让我找到了 [SixXs][sixxs] 这个服务，提供了 `IPv6` 到 `IPv4` 的代理，只要在想要访问的网址后加上 ".ipv4.sixxs.org" 这个后缀就可以了，就像这样：
```
xsounds.org.ipv4.sixxs.org/~haskell/core/$arch
```
抱着尝试的心态添加到了 `pacman.conf` 里，结果真的可用！   

ps: 通过这样的地址下载时就看不到进度条了，原因不明。   
pps: 其实 `SixXs` 也提供了 `IPv4` 到 `IPv6` 的代理服务的，把上面的后缀换成 ".sixxs.org" 或者 ".ipv6.sixxs.org" 就可以了。



[pandoc]: http://johnmacfarlane.net/pandoc/
[repo]: https://wiki.archlinux.org/index.php/Unofficial_user_repositories#haskell-core
[justping]: http://cloudmonitor.ca.com/en/ping.php
[sixxs]: https://www.sixxs.net/tools/gateway/
