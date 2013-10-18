---
layout: post
title: "Windows下编译支持多种脚本语言的64位VIM(GVIM)"
date: 2013-04-21 10:30
comments: true
categories: Vim
---

想要玩一下 [VimIRC.vim](http://www.vim.org/scripts/script.php?script_id=931) 这款插件，不过这货竟然是 Perl 写的，而我用的 vim 没有加入 Perl 支持，所以想不如试试编译一个支持多种脚本语言的 vim             

-   **Vim版本**：7.3.905
-   **需要支持的语言**：Python2/3， Ruby， Perl， Lua， Tcl
-   **编译环境**：Cygwin
-   **操作系统**：64 位的 Windows8

##一.准备工作

#### 1.Cygwin

除了默认的组件外，还需要选择以下的包：autobuild，autoconf，automake，binutils，cmake，gcc-core，gcc,gcc-g77，gcc-g++，gcc4-core，gcc4，gcc4-g++，make，makedepend，pkg-config，gcc-mingw-core，gcc-mingw-g++，gdb，最后，因为现在是编译 64 位版本的 vim 而我又不清楚具体需要哪些包，所以直接搜索 64，选择安装所有的搜索结果 XD
#### 2.各种语言的包

我一律选择了 64 位的版本。Python 分别为 2.7.3 跟 3.3.1。Ruby 本来纠结是用 1.9.3 还是 2.0.0 好，本来电脑里因为配置 Octopress 已经安装了 1.9.3 ,可是 RubyInstaller 似乎只有 2.0 有 64 位的版本，又不知道 Octopress 对 2.0 会不会有兼容性问题。最后选择了下 2.0.0 的 zip 包。Perl 5 的实现选用了 ActivePerl，版本为 5.16.3。Lua 到[这里](http://luabinaries.sourceforge.net/download.html)下载 [lua-5.2.1\_Win64\_bin.zip](http://sourceforge.net/projects/luabinaries/files/5.2.1/Executables/lua-5.2.1_Win64_bin.zip/download) 和 [lua-5.2.1\_Win64\_dllw4\_lib.zip](http://sourceforge.net/projects/luabinaries/files/5.2.1/Windows%20Libraries/Dynamic/lua-5.2.1_Win64_dllw4_lib.zip/download) 这两个文件，解压到同一目录下。       
            
_update (20130501)_：增加对 Tcl 的支持，版本为 [ActiveTcl 8.5.13](http://www.activestate.com/activetcl/downloads/thank-you?dl=http://downloads.activestate.com/ActiveTcl/releases/8.5.13.0/ActiveTcl8.5.13.0.296436-win32-x86_64-threaded.exe)。原本打算用 8.6 的，无奈编译不成功，原因不明。。。

#### 3.VIM 源代码

在这里 [ftp://ftp.vim.org/pub/vim/pc/](ftp://ftp.vim.org/pub/vim/pc/) 下载，当然是选
7.3啦，找到 vim73\_46rt.zip，vim73\_46src.zip 这两个包，前一个是运行时文件后一个是源代码，从 7.3 开始语言文件已经打包进了runtime里。然后再到这里 [ftp://ftp.vim.org/pub/vim/patches/7.3/](ftp://ftp.vim.org/pub/vim/patches/7.3/) 下载补丁。

不过这些补丁实在太多了（911 个！），下载起来实在有点麻烦。最开始想到用 Firefox的DownThemAll 扩展，不过不知为什么下载下来的文件名开头都多了个 2.3（记不太清，反正就是多了几个数字，不过现在想想其实也没什么影响 ←\_←）。然后又想到在资源管理器打开再全选复制，不过速度实在慢的可以。最终 google 后选择了 wget，查了一下命令：

```
$ wget -c -r -nd -np www.xxx.org/pub/path/
```

-c：断点续传；-r：递归目录；-nd：递归是不创建各层目录直接下载到当前目录下；-np：递归时不搜索上层目录。               

把vim73\_46src.zip里的src 文件夹（其他的可有可无）解压到一个暂命名为 make 的目录下，把所有的补丁文件放到与 make 目录同级的 patches 文件夹里。打开 Cygwin，c d到 make 目录下(在 Cygwin 里的 /cygdrive 路径下可以找到系统里的各分区)，执行以下命令打补丁：

```
$ for file in ../patches/7.3.*; do patch -Np0 -i $file; done
```

##二.开始编译

cd 到 src 文件夹下，执行以下编译命令：

```
$ make -f Make_cyg.mak CC=x86_64-w64-mingw32-gcc RC=x86_64-w64-mingw32-windres ARCH=x86-64 PYTHON=/cygdrive/c/Python27 PYTHON_VER=27 DYNAMIC_PYTHON=yes PYTHON3=/cygdrive/c/Python33 PYTHON3_VER=33 DYNAMIC_PYTHON3=yes RUBY=/cygdrive/c/Ruby200_64 RUBY_VER=200 RUBY_VER_LONG=2.0.0 DYNAMIC_RUBY=yes LUA=/cygdrive/c/Lua52 LUA_VER=52 DYNAMIC_LUA=yes PERL=/cygdrive/c/Perl64 PERL_VER=516 DYNAMIC_PERL=yes TCL=/cygdrive/c/Tcl TCL_VER=85 DYNAMIC_TCL=yes -j5 GUI=yes STATIC_STDCPLUS=yes OLE=yes FEATURES=HUGE USERNAME=wenLiangcan USERDOMAIN=Cygwin
```

\*\_VER 跟 \*\_VER\_LONG 的区别是，前者使用不带“.”的版本号，如 200，而后者则需要，如 2.0.0。查看 Make\_cyg.ma k文件可以发现，对 ruby 的支持编译需要通过版本号定位到 \$(RUBY)/lib/ruby/\$(RUBY\_VER\_LONG)/… 这样的路径下，所以对 ruby 使用的这个参数。      

不使用 STATIC\_STDCPLUS=yes 的话，gvim 运行时会需要 libstdc++-6.dll 跟 libgcc\_s\_sjlj-1.dll 这两个文件。          

GUI=yes 就是编译 gvim，GUI=no 则编译 vim。           

多字长跟 cscope 支持都是默认为 yes。         

编译完成后，复制 src/ 下的 vim.exe，gvim.exe，install.exe，uninstal.exe，vimrun.exe 跟 GvimExt/ 下的 gvimext.dll（用来添加系统右键菜单的“Edit with Vim”） 还有 xxd/ 下的 xxd.exe到运行时目录下（由 vim73\_46rt.zip 解压得到）就可以啦。如果需要安装，就在 cmd 里以管理员权限运行 install.exe。

##三.查看成果

运行 vim/gvim ，执行命令：

```
:version
```

![](https://dl.dropboxusercontent.com/s/j558p79x9rnx5cf/vim_version.jpg?token_hash=AAFQm5Bxld04uNOc_j4PaJ_ArAir2S0sI2FDMEEfpjAARA&dl=1)

(￣一￣)y

附上[下载](https://bitbucket.org/wenliangcan/win64-releases-of-vim-with-multiple-languages-surpport/downloads)

##四.补充
+ 中文系统下 cygwin 的 shell 也会自动支持中文，如果编译过程出现错误想要复制 google
一下，英文关键词的搜索结果往往会更准确，所以我想把它改为英文提示，可以在 ~/.bashrc 里末尾加上：
```bash
export LANG='en_US'
export LC_ALL='en_US.GBK'
```
+ 编译后可以执行以下命令清理编译文件：
```
$ make -f Make_cyg.mak clean
```

##五.参考资料
- [windows下编译vim(gvim)并加入python2.6和3.1支持](http://www.vimer.cn/2010/04/windows%E4%B8%8B%E7%BC%96%E8%AF%91vimgvim%E5%B9%B6%E5%8A%A0%E5%85%A5python2-6%E5%92%8C3-1%E6%94%AF%E6%8C%81.html)
- [How to compile 64 bit with cygwin/mingw?](https://groups.google.com/forum/?fromgroups=#!topic/vim_dev/2_bsP-SfnRo)
- [Compiling Vim on Windows](http://people.smu.edu/jrobinet/howto/compile-vim-on-windows.asp)
- [\[转载\]本人在cygwin下完整工作环境的搭建(备忘)](http://www.joynb.net/blog/archives/973)
- [eclipse+cdt+cygwin配置c/c++开发环境](http://blog.csdn.net/jianping_shen/article/details/6300638)
- [使用wget递归下载某目录下的所有文件](http://5iwww.blog.51cto.com/856039/620831)
- [让cygwin使用英文shell界面](http://blog.csdn.net/a221133/article/details/7043318)