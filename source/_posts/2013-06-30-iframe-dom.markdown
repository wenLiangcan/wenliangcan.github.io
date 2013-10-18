---
layout: post
title: "JS 操作 iframe 非跨域引用页面的DOM"
date: 2013-06-30 00:00
comments: true
categories: Javascript
---

偶然发现了学校校园网登录页上验证码的奇葩特性：     
                       
![](https://dl.dropboxusercontent.com/s/6w4hjuewsb8xzbh/CAPTCHA.png?token_hash=AAFpHUTYN6B9vrtHK1plhgeV3N10ZWyFGHPKlx5PsBQeKw&dl=1)      

于是写了个小书签，自动填写验证码并登录：          
```javascript    
javascript:(function(){var a=document.getElementById("main").contentWindow;a.document.getElementById("rand").value=a.document.getElementById("confirmrand").value;a.document.getElementById("login1").submit();})()  
```              
登录信息填写的部分是一个由 iframe 标签引用的非跨域页面，可以通过 *contentWindow* 属性在父页面中对其元素进行操作：        
```javascript   
var a=doucument.getElementById("iframe 标签的ID").contentWindow;   
```                         
                      
##参考资料     

- [javascript访问操作iframe中的元素（不跨域）](http://hi.baidu.com/tang_guangyao/item/464da28619aa49e9b07154dd)