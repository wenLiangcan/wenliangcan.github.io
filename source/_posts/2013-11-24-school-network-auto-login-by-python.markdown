---
layout: post
title: "[Python] 校园网网页验证登录脚本"
date: 2013-11-24 11:14
comments: true
categories: Python
---

依旧毫无技术含量 =。=，走了很多弯路，很多原理也没深入学习，不过还是记录一下吧。。。   

学校的校园网可谓关卡重重，先要锐捷验证一次，然后还要在网页端登录一次，如果一段时间没有网络活动的话又会自动下线（挂 Q 也会）。   

之前写过一个自动填写验证码的[小书签][bookmarklet],后来改成了油猴脚本，让浏览器记住帐号和密码后打开浏览器就能自动登录了。   

可是能不能把这一步也省了，开机登录桌面就能直接上网呢？ 因为有一些开机启动的程序是需要网络的。比如 [Docky][docky] 的天气插件，获取不到数据就会变成一个问号难看死了（主要因为强迫症，每次网页登录后都要手动点击一下 Check Weather 把那个问号变回天气图标，虽然它会定期自动检查。。。），还有就是 Dropbox，因为这个原因我一直都没设置开机启动。   

所以就想看看能不能用 `Python` 来实现这个功能了。   

因为对这一切都毫无概念，所以一开始只能用固有的思维方式考虑问题： 不通过浏览器，就是要用其它语言实现填写文本框然后按确认？或者通过其它语言间接调用 `Javascript` ？ 导致在这个上面绕了好久，看了半天 [`Pyv8`][pyv8], [`Python-Spidermonkey`][spidermonkey] 什么的也没弄懂怎么回事 T\^T

其实填写文本框然后按确认按钮的过程就是填写一个表单（`<form>`），然后浏览器把表单中的数据提交到相应服务器的过程。而 `Python` 或者其它的一些语言都已经有相应的库提供 post/submit 网页表单数据的方法了。所以现在需要做的就是找出 提交数据的地址 和 需要提交的数据项，然后使用对应的方法提交数据。   

提交数据的地址可以由 网页文件中 `<form>` 标签的 `action` 属性找到。   

- 校园网的验证页面地址格式为：`http://address/login.jsp?wlanuserip=xxx.xx.xxx.xx&wlanacip=xxx.xxx.xx.xx`   
- `action` 的值为："/login.do"  
- 所以提交地址就是：`http://address/login.do`   

提交的数据就是 `<form>` 里的 文本框的输入值，有 5 个。两个属性 `type` 为 "hidden" 的 `<input>` 标签，叫做 eduuser 和 edubas，它门的值分别对应验证页面地址中的 wlanuserip 和 wlanacip 的值，前一个是我的内网 ip 地址，后一个没搞明白是啥。   

另外 3 个是 userName1，password1，rand，就是用户名，密码，和验证码。 其中验证码在写小书签时已经知道，它的生成方法其实是由在浏览器中的 `Javascript` 产生一个四位数字，然后向服务器请求一张相应数值的图片。   

小书签里使用 js 获取执行结果，不过现在我要直接 post 数据并不解释网页。既然那个四位数是客户端脚本所产生的，就是说并不由服务端所控制，所以现在我可以伪造这个事实，直接由我向服务器请求一张图片，而且那个数值也不用随机生成，找一个任意确定的四位数就可以了。请求的方式是访问这样一个格式的地址：`http://address/common/image.jsp?rand=xxxx`   

其实到这里我原本的想法是，服务器应该会把对验证页面地址和验证码图片地址的访问作为同一个 session，就是服务器会记录下你请求了的图片的验证码数值，在提交表单时里面的 rand 项的值必须与之相符才能通过验证（关于 session 什么的我不太知道是不是该这么表达）。 可事实是，那个验证码图片的请求不是必须的，就是说我可以直接随便提交一个四位数作为 rand 的值也能通过验证！！！ 这个页面的验证码的部分除了那张图片，全都是由客户端 js 来完成的 Σ(っ  °Д °;)っ   

整个脚本所做的事情就是，提交一个包含验证信息的表单。

```python autologin.py
#!/usr/bin/env python

import requests, time

url = "http://address/login.do"
info = {'userName1': '...',
        'password1': '...',
        'rand': '2222',
        'eduuser': '...',
        'edubas': '...'
       }

while True:
    requests.post(url, data = info)
    time.sleep(65)
```

这里用了 [`requests`][requests] 这个模块，设置每 65 秒验证一次防止自动下线（表问我为神马是 65 而不是 60）。   

ps:   

过程中还找到了 Firefox 的一个扩展 [HttpFox][httpfox]，可以用来分析 HTTP 网络活动，下面是一张从访问验证页面到提交信息的记录截图：   
![http]   
通过它才发现我原本还遗漏了 eduuser 和 edubas 两个数据项。   

另外，点击下面的 "raw"，可以看到还能通过把数据嵌入到 url 地址的方式提交表单：   
![raw]   
url 的格式为： `http://address/login.do?userName1=xxx&password1=xxx&rand=xxxx&eduuser=xxx&edubas=xxx`   

pps:   

接下来打算把我的树莓派设置成路由器，用这个脚本进行验证（话说买回来没多久就闲置了 -________-''   

还有不知要不要写成 Daemon 的形式？或者学一下写个 Systemd 的 Service 来运行？现在暂时用 Openbox 来自动执行。   

不过还都是打算，我的拖延症又犯了。。。   


##参考资料
- [如何用python提交一个web表单][how-to-use-python-submit-a-web-form]
- [Requests Quickstart][quickstart]
- [HTML < form> Tag][form_tag]



[bookmarklet]: /blog/2013/06/30/iframe-dom/
[docky]: http://www.go-docky.com/
[pyv8]: http://code.google.com/p/pyv8/
[spidermonkey]: http://code.google.com/p/python-spidermonkey/
[requests]: http://docs.python-requests.org/en/latest/
[httpfox]:https://addons.mozilla.org/En-us/firefox/addon/httpfox/
[how-to-use-python-submit-a-web-form]:http://www.averainy.info/how-to-use-python-submit-a-web-form/
[quickstart]:http://docs.python-requests.org/en/latest/user/quickstart/
[form_tag]: http://www.w3schools.com/tags/tag_form.asp

[http]: https://dl.dropboxusercontent.com/s/47sr6ye8bu1vuxs/http.png?dl=1&token_hash=AAHk_E3lc7x88hOB6t0fMSLdXRf7z5otjfQN-rbNTDahIw
[raw]: https://dl.dropboxusercontent.com/s/uuhxpexkwq5bf3f/raw.png?dl=1&token_hash=AAHdusJ29VHNtLIInwRFziMi6vuNlFwEkM6efKlGepcUew
