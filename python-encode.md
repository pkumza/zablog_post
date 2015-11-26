title: python编码与中文显示
description: Python的编码，python中文操作和显示。
categories: 学习笔记
tags: [Python]
date: 2014-07-21 17:40:21
---
#本文目的
一直以来，在学习和实验中要经常使用python，但是python和中文仿佛是一对死对头，经常出现各种问题。以前也尝试解决，把问题应付过去，程序可以运行就到此为止了，而过了一段时间又忘记了。虽然是一个小问题，还是在这里理清楚比较好。
简单地说，很可能出现这种情况：新建一个test.py文件，使用notepad打开，键入
```python
print '5mark你好'
```
保存，再打开cmd，找到相应的文件夹，然后键入
```bash
python test.py
```

<!--more-->

然后就悲剧了，系统提示了Syntax Error，内容如下：
```bash
C:\Users\Marchon\Desktop\tek>python test.py
  File "test.py", line 1
SyntaxError: Non-ASCII character '\xc4' in file test.py on line 1, but no encodi
ng declared; see http://www.python.org/peps/pep-0263.html for details
```
环境呢，首先是Windows的环境，因为自己编写的python程序比较短小而且比较简单，所以就直接使用notepad++作为工具了。
python安装的是python2.7.6。
###Tips：
我们知道，python是一种对于缩进的要求非常非常严格的语言。如果缩进用Tab，可以，那么必须所有的都使用Tab。以前，还是喜欢在python中运用Tab，但是在Debug的过程中一定会伤透脑筋，因为Tab和空格混合的话，是很难查验出来的，一旦出问题就乱套了。所以，一定要尽量避免在python代码中使用Tab。在Notepad++中编辑python语言，一定要把Tab设置为4个空格。设置方法是：
```python
设置->首选项->编辑->指标符设置
```
这样，在写作的时候，方便进行缩进控制，也不会出现混乱的情况。
当然，4个空格是我个人的喜好，如果你愿意，你也可以设置为3个空格、8个空格，或者其他情况。不过一般也还都是4个吧。
#编码
如果不知道什么是“字符集”、什么是“编码”，那么就可以不要继续看了，最好移步一些相关的课本或者博客，先搞懂什么是ANSI，ASCII，Unicode，UTF-8这些。我觉得有一篇文章介绍得很好，[点我跳转查看](http://blog.csdn.net/xiongxiao/article/details/3741731)。
默认情况下，Windows中多数都是GBK的编码方式，无论是中文文件名，还是cmd命令行工具中。
首先，我们按下**Windows+R** ，打开命令行工具，右击标题栏，点击属性。
可以看到，当前代码页：936（ANSI/OEM - 简体中文GBK）。
所谓936，是*代码页code page*，也就是cp936（如果你听说过的话）。GBK对应的code page就是CP936。
#操作
回到开始。开始没有办法直接使用，是因为python默认只认识ASCII的那些基础字符，里面只要含有越界的字符，甚至无论是不是出现在注释里，都会立刻翻脸。
##解决方法
最简单的方法，就是，在开头加上一段声明，test.py变为了
```python
# -*- coding:utf-8 -*-
print '5Mark你好'
```
这样就可以运行了。这次到cmd中运行，可以正常输出中文字符。
然而实际上，“5Mark你好”这个字符串，在这里，在utf-8的状态下，并不是“5Mark你好”。
就相当于，这个文件在开头说了“我是utf-8编码的”，然后让python承认自己可以有这些编码，然后拿着GBK的“5Mark你好”，糊弄过去了而已。在python的逻辑里处理过之后，print命令把这个糊弄过去的字符串打印到了cmd中。而cmd又是GBK编码的，所以正常显示出了**5Mark你好**这句话。
如果不信的话，我们来做一个尝试。
###折腾1
新建一个文件test2.py空文件，然后在notepad++中，点击格式，选择以UTF-8格式编码。
这个时候再次键入一下内容
```python
# -*- coding:utf-8 -*-
print '5Mark你好'
```
保存，运行，会出现什么呢？
运行结果：
```bash
I:\tek>python test2.py
5Mark浣犲ソ
```
这明明是正格儿的UTF-8编码，怎么出来就不对了呢。
这还是因为cmd是GBK编码的，正格儿的UTF-8输入python，再转回cmd，自然就看不懂了。
如果接受方不是cmd，而是文件。我们尝试把一段正格的UTF-8字符串，传入一个文件。然后再使用notepad++打开，会发现编码方式是UTF-8，而且内容正常。
###折腾2
如果把cmd的字符集改了是不是就可以了呢？
65001是UTF-8的code page
首先打开cmd.exe，然后键入命令：
```bash
chcp 65001
```
这个时候再运行刚才的python，虽然不是浣犲ソ了，但是变成了别的东西，依然看不懂。
这并不是因为编码问题，而是现在cmd的点阵字体不支持。
命令行标题栏上，右键，选择属性，字体，然后把自体修改为"Lucida Console"，应用。
再次运行刚才的程序，就可以成功地输出 **5Mark你好** 了。

做完这些之后还是赶紧运行命令
```bash
chcp 936
```
把字符集还原回来。否则，以后出什么bug，可能就把改过字符集的事情给忘掉了。
###折腾3
必须要 
```python
# -*- coding:utf-8 -*-
```
 这句话吗？
其实不是必要的。
可以在notepad++，格式中选取utf-8。
记得是UTF-8，不是UTF-8无BOM格式。
所谓BOM，是文件前头，用来标识文件内部编码方式的开头。如果有BOM，那么内部即使没有这条注释，python也可以认定此文件是utf-8的文件，因而采用utf-8的方式来读取。这时就不用怕无法读取汉字了。
所谓的“UTF-8无BOM格式”，就是采用UTF-8来编码，但是开头没有BOM。因为没有BOM，一般的编辑器也好，各种程序也好，很容易把这个文件识别为ANSI，这样就容易出现乱码，加载的时候会出错的。
当然，UTF-8是向下兼容ANSI的，就算是普通的全英文字符的ANSI，标注成了utf-8格式，读取起来也不会出现问题的。
#转换
python其实是可以处理各种编码的，当然这需要转换。
这种功能是必须的，因为当使用python来处理文本，尤其是处理网络相关的东西的时候，不同编码是不会难倒它的。
下面有一份示例代码：
```python
# -*- coding: utf-8 -*-  
import urllib  
import sys  
  
if __name__ == '__main__':  
    if len(sys.argv) > 1:  
        str = sys.argv[1]  
        str = unicode(str, 'gbk')  
    else:  
        str = "5Mark你好"  
		
    print str  
    params = {}  
    params['name'] = str.encode("UTF-8")  
  
    print urllib.urlencode(params)  
```
因为cmd使用的是GBK，首先要转化为unicode的，然后需要使用url encode的时候，转化为utf-8。
#总结
因该说这些编码什么的，python什么的，还是挺入门的。随手一记，希望能有一点点作用。