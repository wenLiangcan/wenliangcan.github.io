---
layout: post
title: "如何在 Python 中操作正则替换的匹配结果"
date: 2014-08-02 21:39
comments: true
categories: [Python, Regex]
---

在进行字符串的正则替换时，我们可能会希望替换的结果是基于匹配结果的，就是说把匹配的字符串提取出来进行一定的修改再应用回原文。   

以 `sed` 为例：
```bash
$ echo 'cat dog mouse' | sed 's/dog/\*&\*/'
cat *dog* mouse
```
用 `&` 符号可以把匹配的字符串 `"dog"` 提取出来，然后在它的前后加上了 `*`。   

在 `Python` 使用 `re` 模块时可以这么写：
```python
import re

def repl(m):
    return '*' + m.group() + '*'

print(re.sub('dog', repl, 'cat dog mouse'))

# 输出：cat *dog* mouse
```
当传给 `re.sub()` 方法的第二个参数是一个函数时，`re.sub()` 会给这个函数传入一个 `Match Object` 然后以这个函数的返回值作为替换结果。   

我们可以通过这个 `Match Object` 的 `grou()` 方法获取每次执行替换前的匹配结果。这个方法之所以叫做 `group` ，是因为我们有时会使用多个匹配模板，同时获取多个（一组）匹配结果，再以 `sed` 为例：
```bash
$ echo 'cat dog mouse' | sed 's/\(dog\) \(mouse\)/\*\1\* _\2_/'
cat *dog* _mouse_
```
符号 `\1` 和 `\2` 对应于第一和第二个括号里我们要提取的字符串。   

Python 则如下：
```python
import re

def repl(m):
    return '*' + m.group(1) + '*' + \
    '_' + m.group(2) + '_'

print(re.sub('(dog) (mouse)', repl, 'cat dog mouse'))

# 输出：cat *dog* _mouse_
```
上一例中的 `m.group()` 也可以写为 `m.goup(0)`，意为把所有结果作为一个字符串取出。   


还有一种跟传统正则表达式比较相似的写法：
```python
import re

def repl(m):
    return '*' + m.expand(r'\1') + '*'

print(re.sub('(dog)', repl, 'cat dog mouse'))
```
用 `expand()` 方法展开表达式，不过似乎不支持符号 `&`。   


##参考资料
- [Regular Expression HOWTO][pydoc]
- [Regex in python: is it possible to get the match, replacement, and final string?][SOF]
- _[Python Cookbook (3rd Edition)][book]_: **2.6. Searching and Rplacing Case-Insensitive Text**



[pydoc]: https://docs.python.org/3.4/howto/regex.html
[SOF]: https://stackoverflow.com/questions/9134964/regex-in-python-is-it-possible-to-get-the-match-replacement-and-final-string
[book]: http://shop.oreilly.com/product/0636920027072.do

