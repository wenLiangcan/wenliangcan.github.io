---
layout: post
title: "去除全屏下 GVim 出现的白边"
date: 2014-07-26 14:08
comments: true
categories: [Vim, Linux]
---

全屏下 GVim 的左侧和底部会出现白边，可以在 `~/.gtkrc-2.0` 文件中加入一下设置：
```
style "vimfix" {
    bg[NORMAL] = "#002B36"
}
widget "vim-main-window.*GtkForm" style "vimfix"
```   
`#002B36` 这个颜色对应于我使用的 `Solarized Dark` `Vim` 配色的背景色，可以根据个人情况修改。   



##参考资料
- [howto fix gvim border width ?][link]



[link]: https://bbs.archlinux.org/viewtopic.php?pid=786106#p786106
