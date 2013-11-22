---
layout: post
title: "Arch Linux 下搭建 MonoGame 开发环境"
date: 2013-11-22 15:11
comments: true
categories: [Arch Linux, C#, Mono, MonoGame]
---

####其实就是安装几个包而已：

```bash
$ yaourt -S monodevelop-git monogame monodevelop-monogame-addin-git
```

第一个是 IDE，`Extra` 仓库里的更新太慢了，还停留在 `4.0.13`，`MonoDevelop` 的稳定版现在是 `4.2.1`。   


最后那个是 `MonoDevelop` 的 `MonoGame` 插件，有了它才能在 `MonoDevelop` 里创建 `MonoGame` 的解决方案。话说这个当初把我给搞蒙了，看[教程][tutorial]以为直接安装好 `MonoGame` 就可以了，看了 [`aur`][aur] 里的评论才知道这么个东西。觉得维护者应该把它加到可选依赖里。   

####现在试着创建一个 Solution：   
- 在菜单栏选择 File -> New -> Solution
- 接着选择 C# -> MonoGame -> MonoGame Linux Application   
![](https://dl.dropboxusercontent.com/s/rw8l51u6es27pwi/new_solution.png?dl=1&token_hash=AAGKLEegUiW1nYqUbM67-AX-ZorFtbwGqsFLOQb3U2efNg)  

- 命名并保存   
![](https://dl.dropboxusercontent.com/s/8mkdatzfftk49pw/monogame_sln.png?dl=1&token_hash=AAFMSNjCaOBtuzZPYxjAW-_N_tSRdJ8V1gsLYn_0r1ARFw)  
(编辑器的配色可以在 Edit -> Preferences -> Text Editor -> Syntax Highlighting 里设置)   

- 在编译运行应用前可以把第 23 行的 `graphics.IsFullScreen` 设为 `false`，全屏运行的话会带来一些不便。   
![](https://dl.dropboxusercontent.com/s/l9ic9t3pwq8jifx/code.png?dl=1&token_hash=AAGdbz1QdMFfZq232ndgEoiIzVujxXJDqRYi3q6r_VnKTg)   

- 按 F5 或 选择菜单 Run -> Start Debugging 启动程序   
![](https://dl.dropboxusercontent.com/s/rbk8w0vmc5c3upb/app.png?dl=1&token_hash=AAGgKRA-3BdwOy2kQNBjvhP8f7w0DpNPzEU9PbwSSJ8jfg)   
不太有趣 \_(:3」∠)_

####为了体验最新的特性，还可以通过 `MonoDevelop` 的 `VersionControl` 插件下载最新的 `MonoGame` 代码用来创建应用：
详见：[Getting and Building the Latest Sources (develop3d)][src] 和 [Creating a MonoGame Application (develop3d version)][app]

##参考资料
- [Tutorials:Getting Started Guide Linux][tutorial]



[tutorial]: https://github.com/mono/MonoGame/wiki/Tutorials%3AGetting-Started-Guide-Linux
[aur]: https://aur.archlinux.org/packages/monogame/
[src]: https://github.com/mono/MonoGame/wiki/Tutorials%3AGetting-Started-Guide-Linux#wiki-Getting_and_Building_the_Latest_Sources_develop3d
[app]: https://github.com/mono/MonoGame/wiki/Tutorials%3AGetting-Started-Guide-Linux#wiki-Creating_a_MonoGame_Application_develop3d_version