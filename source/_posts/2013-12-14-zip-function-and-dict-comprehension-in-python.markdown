---
layout: post
title: "zip 函数和 Dict Comprehension"
date: 2013-12-14 16:43
comments: true
categories: [Python, Images]
---

要下载某个会反盗图的网站上的图片，想到了这篇 [Google 的图片代理][img_proxy]。   

通过 Google 来抓取想要的图片就可以逃过反盗图了，主要的工作就是为一系列原图的 url 生成特定的请求链接，所以要做的就是拼凑字符串，机械的任务当然交给机器去做了，`Python` 实现之。   

虽然听起来挺简单，可是以我原有的知识实现起来还是有些繁琐。 需要处理的数据是存储在一个列表里的原图链接，最终想要获得的数据是一系列的文件名和与之一一对应的下载链接，第一反应当然是用字典组织它们了。 请求地址是从原链接拼凑生成的，文件名也是由原链接获得，使用 `os.path.basename()` 方法。   

开始的想法是用两次 `List Comprehension`，遍历存储原链接的列表，获得两个分别含有文件名和下载地址的新列表，可是怎么把它们分别作为 `Key` 和 `Value` 合成为一个字典呢？我可不要循环操作下标。。。。Google 之，知道了 `zip` 函数，用法如下：
```python
>>> keys = ['a', 'b', 'c']
>>> values = [1, 2, 3]
>>> dictionary = dict(zip(keys, values))
>>> print dictionary
{'a': 1, 'b': 2, 'c': 3}
```   
`zip()` 会返回一系列的元组，然后通过 `dct()` 生成一个字典。

可是两次遍历的都是同一个列表，难道就不能在同一次遍历中完成上面的任务吗 ("▔□▔)，好吧，原来还有 [`Dict Comprehension`][pep274] 这个东西：   

- 如果需要遍历的 `sequence` 是一个列表，那么
```python
d = {l : l for l in list}
```
- 如果 `sequence` 是一个字典
```python
d = {key : value for (key, value) in dictionary}
```
- 另外，在 2.6 及以下的 `Python` 中，需要使用 `dict` 构造函数
```python
d = dict(... for ... in sequence)
```   


再复习一下如何用 `for in` 迭代一个字典分别获取其中的键和值：
```python
for key, value in d.iteritems():
    ...
```
在 `Python 3.x` 中使用的是 `items()`, `Python 2.x` 中也有这个方法，不过返回的是包含一对键、值的元组。   

最后，贴一下代码：
```python
#!/usr/bin/python

import urllib2, sys, os

def inputlinks():
    if len(sys.argv) == 1:
        links = raw_input("Enter links :\n").split()
    else:
        if sys.argv[1] == "-i":
            with open(sys.argv[2], 'r') as f:
                links = f.read().splitlines()
        else:
            links = sys.argv[1:]
    return links

def generate(links):
    prefix = r"https://images2-focus-opensocial.googleusercontent.com/gadgets/proxy?url="
    suffix = r"&container=focus&gadget=a&no_expand=1&resize_h=0&rewriteMime=image%2F*"
    images = {os.path.basename(i) : prefix + i + suffix for i in links}
    return images

def download(images):
    for name, image in images.iteritems():
        try:
            imgdata = urllib2.urlopen(image).read()
            with open(name, 'wb') as output:
                output.write(imgdata)
        except:
            print "Er..."

if __name__ == "__main__":
    download(generate(inputlinks()))
```


##参考资料
- [How do I download a zip file in python using urllib2?][basename]
- [Python文件读写][split_lines]
- [Map two lists into a dictionary in Python][zip]
- [Python: create a dictionary with list comprehension][dict]
- [PEP 274 -- Dict Comprehensions][pep274]
- [Iterating over Dictionaries…For Loops in Python][iterate]
- [Google 的图片代理][img_proxy]



[img_proxy]: http://qixinglu.com/post/google_image_proxy.html
[pep274]: http://www.python.org/dev/peps/pep-0274/
[basename]: http://stackoverflow.com/a/4028894/1436873
[split_lines]: http://www.cnblogs.com/cacique/archive/2012/08/02/2617440.html
[zip]: http://stackoverflow.com/a/209854/1436873
[dict]: http://stackoverflow.com/a/1747827/1436873
[iterate]: http://stackoverflow.com/a/3294899/1436873
