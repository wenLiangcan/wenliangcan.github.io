---
layout: post
title: "修正图片文件扩展名的 Python 脚本"
date: 2013-10-18 16:32
comments: true
categories: Python
---

有时候从往上下再下来的图片会出现扩展名与实际的图片格式不一致的情况，在 Linux 里我用 [Eye of GNOME][0] 看图，就会因为这样而无法显示。     
            
最近尝试自学 Python，查了一下相关资料后试着写了一个识别图片格式并更正扩展名的脚本。       

###1. 识别图片格式       
不看扩展名如何知道一个图片文件是什么格式呢？ 网上说的比较多的是通过检查[文件头][1]标志的方法来判断图片格式，可是自己写的话感觉有点麻烦。

其实已经有现成的轮子可以用了，就是 ``imghdr`` 这个模块，可以看一下它的 Docstring 的说明       
```python
>>> import imghdr
>>> print imghdr.__doc__
Recognize image file formats based on their first few bytes.
```

只能识别一些常见的图片格式，对一些特殊的图片文件比如 svg， psd 就无力了，不过已经满足我的需求啦。    

用法：
```python
imghdr.what(<image_file>)
```
返回对应格式的扩展名字符串。    

###2. 更正文件扩展名
需要用到一些文件操作的方法，用到 ``os`` 这个模块，记录一下：    


- ``os.chdir(dirname)``: 用于改变工作路径到 dirname。
- ``os.listdir(dirname)``: 列出 dirname 下的目录和文件。
- ``os.path.isfile(name)``: 判断 name 是否为文件。
- ``os.path.splitext()``: 分离文件名与扩展名。
- ``os.rename(a, b)``: 重命名文件。

   

###3. 脚本

```python correct-image-file-extension.py https://gist.github.com/wenLiangcan/7040164
#!/usr/bin/python
 
import os, sys, imghdr
 
#允许指定文件目录，默认则检测当前目录
if len(sys.argv) == 2:
    try:
        os.chdir(sys.argv[1])
    except OSError, e:
        if e.errno == 2:
            print "No such file or directory: '%s'" % sys.argv[1]
 
for i in os.listdir('.'):
    if os.path.isfile(i):
        name = i
        #有时候会出现 ".png.jpg" 这样格式的扩展名，为了美观，作相应的调整。
        while os.path.splitext(os.path.splitext(name)[0])[1] != '':
            name = os.path.splitext(name)[0]
        fmt = imghdr.what(i)
        xname = os.path.splitext(name)[1][1:].lower()
        if fmt != None and fmt != xname:
            #JPEG 图片的扩展名有时会简写为 jpg，而 imghdr 里则表示为 jpeg，遇到这样的情况直接跳过不再更改。
            if fmt == 'jpeg' and xname == 'jpg':
                continue
            cn = os.path.splitext(name)[0] + '.' + fmt
            os.rename(i, cn) 
            print 'Renamed %s to %s' % (i, cn)
```

##参考资料   
- [How to check if a file is a valid image file?][2]
- [How to rename a file using Python][3]
- [python天天进步(2)--文件操作之遍历目录][4]



[0]: http://projects.gnome.org/eog/ "Homepage"
[1]: http://baike.baidu.com/view/1606305.htm
[2]: http://stackoverflow.com/a/902779/1436873
[3]: http://stackoverflow.com/a/2491232/1436873
[4]: http://www.cnblogs.com/vivilisa/archive/2009/03/01/1400968.html
