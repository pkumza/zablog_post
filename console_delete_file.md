title: 如何删除以-开头的文件
date: 2016-05-09 09:39:39
categories: ['技术指南']
tags: ['Unix']
---

今天删除文件的时候遇到一个问题。当我准备清理一个APK列表的时候，发现rm命令不好用了。

```bash
zach$ sudo rm -f *
rm: illegal option -- -
usage: rm [-f | -i] [-dPRrvW] file ...
       unlink file
```

我开始不知道发生了什么。我一直以来使用的rm命令用错了？
后来才发现是下面这个原因。

```bash
zach$ ls | grep "^-"
--9xmvsPkepA4XhroPEFVscy.png
-xf7j9NNrVbMHTUnqfmw1UiG.apk
```

原来是有两个文件是以-打头，导致rm失败。
当使用命令rm的时候，接上了这样一个倒霉的文件名，那么rm命令会认为-x是一个参数。然而rm -x是无意义的，所以会报错。

```bash
zach$ rm -xf7j9NNrVbMHTUnqfmw1UiG.apk 
rm: illegal option -- x
usage: rm [-f | -i] [-dPRrvW] file ...
       unlink file
```

解决方法很简单。

第一种方法：

```bash
$ rm ./-xf7j9NNrVbMHTUnqfmw1UiG.apk
```

第二种方法：

```bash
$ rm -- -xf7j9NNrVbMHTUnqfmw1UiG.apk
```

最终我删除这些apk的命令是：

```bash
zach$ rm -r -f -- *.apk
```

这个虽然没有什么技术含量，但是稍微做一点小记录，防止下次再犯。


##### PS
最后的考一个小问题：
刚才讲了如何删除一个以“-”开头的文件，那么如何建立一个以“-”开头的文件呢？

```bash
# This command does not work.
touch "--a"

```

以上这条命令是不会奏效的。
其实答案很简单的，自己思考一下吧。
