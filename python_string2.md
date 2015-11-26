title: Python 字符串处理2
description: Python的字符串处理。
categories: 学习笔记
tags: [Python]
date: 2015-02-17 20:13:00
---
##字符串合并
最简单也最直观的字符串合并，是采用加号+。
当然，在大多数的情况下，并不存在太多性能上的问题。在python中，字符串是没有办法拆分和修改的，所谓的+，只不过是不断生成新的字符串罢了。因此在计算a+b+c的时候，首先要计算b+c扔掉b和c，再计算a+(b+c)。扔掉b+c以及a。
举个例子：
    >>> import operator
    >>> print reduce(operator.add,["a","bb","ccc"],'000')
    000abbccc
同时我们也可以采用join函数把字符串连接起来，比如：
    ''.join(['a','bb','ccc'])
当然我们还有另外一种方法就是使用%，下面简单举个例子就略过：
    >>> print '%s %s' % ('%d' % 123,'321')
    123 321

<!--more-->
    
这篇就是这么短，并没有什么额外内容。