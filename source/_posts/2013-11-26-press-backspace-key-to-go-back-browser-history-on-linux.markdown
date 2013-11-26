---
layout: post
title: "Linux 下浏览器里使用退格键返回上一页"
date: 2013-11-26 20:44
comments: true
categories: [Linux, Browser, Firefox, Chrome]
---

这里只针对 Firefox 和 Chrome，Linux 下它们默认都不能用退格键作为返回历史上一页的快捷键，Opera 倒是可以。   

***Firefox*** :   
在 about:config 里查找到 "browser.backspace_action"  这一项，把它的值设置为 0 就可以了。1 代表向上滚动页面（此时要向下滚动的话可以按 Shift 加退格键），我的 fx 默认被设为了 2，按退格键没任何反应。   

***Chrome*** :   
需要安装一个扩展，叫做 [Backspace As Back/Forward for Linux][extention]。安装后在它的设置页勾选 "Activate Backspace for navigation in history object" 这一项，里面还可以设置例外，对某些网页不启用这一功能。   


##参考资料
- [Set Backspace's Firefox behavior][life_hacker]
- [解决浏览器[←Backspace]键失灵的方法][bbs]



[extention]: https://chrome.google.com/webstore/detail/aeffggjddcchloadflonilaahpclmbnm
[life_hacker]:http://lifehacker.com/269945/set-backspaces-firefox-behavior
[bbs]: http://forum.ubuntu.com.cn/viewtopic.php?f=48&t=349227