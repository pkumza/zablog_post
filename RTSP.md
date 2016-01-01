title: Android播放RTSP流
date: 2014-12-16 12:46:23
categories: ['技术指南']
tags: [Android, RTSP]
---

Android自带的mediaPlayer播放rtsp，实际上播放的只是rtsp的刘文建，并不能播放实时流，为了解决播放实时流的问题，尝试使用[Vitamio](http://www.vitamio.org/)产品来帮助自己完成这一构想。
RTSP有什么用？最简单的，网上可能会有一些rtsp视频流，只要输入这个地址就能看视频，像点播[youku](www.youku.com)、[youtube](http://www.youtube.com/)一样，岂不快哉。
另外，RTSP最重要的是一种技术标准。通过一些其他终端产生的数据源信息，可以通过RTSP传导到另一个终端。最简单的是采用手机进行监控视频的接受，就很有可能采用RTSP协议。下面是如何搭建这样一个内容。

# Step1: 在Windows系统下搭建RTSP的流服务器，并且采用手机软件播放。

这个内容主要参考了[csdn的blog](http://blog.csdn.net/beitiandijun/article/details/9232405)，教程的版本和我安装的版本不是完全一致，我安装的版本是2.1.5Windows版本。然后和教程不一样的地方，也是我卡壳很久的地方是，在选择新目标的时候，选择好RTSP之后，要点击添加，这个时候才能设置具体的端口名称和路径名称。

![Windows7下，服务器的情况：](http://imglf1.nosdn.127.net/img/MGpGUW9CdGlzcDdBYzRBSWhKOEJmZUFESDhpMk8vTzIzYW43cVg4eTJ1U2ZOcVdQOS9XaEpRPT0.png?imageView&thumbnail=1680x0&quality=96&stripmeta=0&type=jpg%7Cwatermark&type=2&text=wqkgWmFjaGFyeSAvIG1hcmNob24ucGt1b3Mub3Jn&font=bXN5aA==&gravity=southwest&dissolve=30&fontsize=340&dx=16&dy=20&stripmeta=0)

# Step2: 使用另外一台电脑测试这个服务器

![Windows8.1，链接Windows7的源，进行播放的情况：](http://imglf2.nosdn.127.net/img/MGpGUW9CdGlzcDdBYzRBSWhKOEJmVEh2bzNoOW9BenBuZnJVWnlXeTRGQ1RNSDZ1V295T3lBPT0.png?imageView&thumbnail=1680x0&quality=96&stripmeta=0&type=jpg%7Cwatermark&type=2&text=wqkgWmFjaGFyeSAvIG1hcmNob24ucGt1b3Mub3Jn&font=bXN5aA==&gravity=southwest&dissolve=30&fontsize=340&dx=16&dy=20&stripmeta=0)

# Step3: 使用VLC手机软件测试服务器

![手机使用VLC手机端播放的情况：](http://imglf1.nosdn.127.net/img/MGpGUW9CdGlzcDdBYzRBSWhKOEJmZG12VDNEbk5nN2ZUNGNrbjd1T2cwdWt4NVpwQ3lDN1hnPT0.png?imageView&thumbnail=1680x0&quality=96&stripmeta=0&type=jpg%7Cwatermark&type=2&text=wqkgWmFjaGFyeSAvIG1hcmNob24ucGt1b3Mub3Jn&font=bXN5aA==&gravity=southwest&dissolve=30&fontsize=340&dx=16&dy=20&stripmeta=0)

# Step4: 自己修改手机apk，使得手机可以浏览自定义RTSP源

![更改APK关键代码](http://imglf0.nosdn.127.net/img/MGpGUW9CdGlzcDdBYzRBSWhKOEJmZThrVk9yS1hRYWx0WlhFb1Z0eFI2cDJienREZnRoS1lBPT0.png?imageView&thumbnail=1680x0&quality=96&stripmeta=0&type=jpg%7Cwatermark&type=2&text=wqkgWmFjaGFyeSAvIG1hcmNob24ucGt1b3Mub3Jn&font=bXN5aA==&gravity=southwest&dissolve=30&fontsize=340&dx=16&dy=20&stripmeta=0)

![采用自己创建的apk播放RTSP流的效果](http://imglf1.nosdn.127.net/img/MGpGUW9CdGlzcDdBYzRBSWhKOEJmVXlXbnhreDRjOWZWNDBrQkd6Q3NyOE9VaWZRMm5qTDZRPT0.png?imageView&thumbnail=1680x0&quality=96&stripmeta=0&type=jpg%7Cwatermark&type=2&text=wqkgWmFjaGFyeSAvIG1hcmNob24ucGt1b3Mub3Jn&font=bXN5aA==&gravity=southwest&dissolve=30&fontsize=340&dx=16&dy=20&stripmeta=0)

# 总结：

现在已经可以说明手机播放RTSP源是没有问题的了。
从可行性来说，作为播放端的应用程序，应该只需要RTSP的源地址，端口号，路径就可以实现播放。然后只要相应的编码方式符合标准，并且具有这种解压技术就可以了。
对邮件中提到的内容，客户端播放没有太大的问题。但是有可能出现声音和图像不完全同步，有一两秒的延迟的情况，需要研究方法解决。
重点在于，邮件中列出的大量内容基本上都是服务器端，也就是编码端要进行的工作，而不是解码端（客户端）所需要注意的东西。现在做的测试RTSP协议的Demo，是采用电脑搭建的服务器，并没有对这些进行限制，都是采用最为默认的设置。我想知道的是，为了实现这个APP，如果只是实现Android端就可以了，那么工作量应该不是特别多。关于这项工程，我们需要去做服务器端吗？如果需要做的话，就会特别麻烦，因为需要知道提供的条件，而且对于这方面也不是很了解。网上有一些把视频流转化为RTSP流的教程，不过器械比较局限，是专门根据一些设备（譬如Android手机）来设计的。