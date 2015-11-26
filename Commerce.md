title: 搭建电商网站实战1：安装与设置
date: 2014-06-13 21:45:30
categories: 技术指南
tags: [Drupal]
---
#安装
有两种安装方法。
##基于已有Drupal网站的方法
首先下载配置好Drupal，然后下载Commerce模块，为了安装这个模块，还需要安装
- Ctools
- Entity API
- Views
- Rules
- Address Filed

这几个模块。慢慢装去吧少年。
我确实也这么做了，而且也弄出来了。不过这次，就算了，实在懒得在这个页面把整个过程描述一遍。所以，采用下面这个方法
<!--more-->
##安装Commerce Kickstart 2
因为上面安装需要一大堆模块，所以可能有些人会接受不了。没有关系Commerce Guys这个组织，也就是Commerce模块的创立者，他们做了一个大礼包，包括了Drupal7的core核心，包括了必须的很多模块，包括了一些不是必须的但是常用的选用模块，当然也包含着我们重中之重Commerce模块。
不过看本文的话，还是需要你有Drupal的经验，否则你连Drupal都不会装的话，看下面也许会有一些难度。当然，如果你努力克服的话，也不要紧。
###系统要求
PHP5,SQL,Apache等等，这些要求就不细说了，因为这次安装是采用了XAMPP，直接装这个就可以了，没管别的。
下载好XAMPP后，把SQL和Apache打开，在浏览器输入localhost就可以访问XAMPP的管理界面了。
点击PhpMyAdmin，然后新建个数据库，这里取一个名字叫做Kick。
![phpMyAdmin](http://i13.tietuku.com/ac16b30b8f870b01.png)
然后下载好Kickstart，解压到XAMPP下面的htdoc文件夹下，也叫做kick。
![下载](http://i13.tietuku.com/daa9bc50bfe1be80.png)
到浏览器输入localhost/kick，访问界面如下
点击Let's Get Started!。然后，屏幕白了，好慢啊，怎么会这样。打开浏览器的开发，才发现是因为所有的时间都耗在努力连接Google上去了。为什么要连接Google呢，因为外国的网站一般一个习惯，就是使用Google的字体库、主题库、CSS、Ajax、JQuery什么的。有一个小东西需要了，就到Google的链接下去下载。但是，问题在于，Google在天朝的服务并不稳定，而且最近几天几乎全抽抽了，根本下不下来。没有办法，或者去源代码里把Google屏蔽了，要不然就是在浏览器里把Google屏蔽了，或者就是我选择的方法，把网断了。这一招是立即见效的，断了网，立刻就显示出来了。实际上，没有Google主题，显示照样很好看。
![开始安装页面](http://i13.tietuku.com/4c1d0a9464a82eca.png)
![google源的下载失败的主题](http://i13.tietuku.com/9eb13d8d7737ffff.png)
这时候会有两个错误框，很简答只要照着做就行了，分别建立files文件夹，并弄好settings.php。![两个错误](http://i13.tietuku.com/cea806972ede3567.png)建立files文件夹后用chmod -R 777 files来指定权限，同样的settings.php也需要指定权限。
再次点击就可以进入下一步了。
数据库，输入刚才的数据库，用户名密码，默认一般是root，然后没密码。
![数据库](http://i13.tietuku.com/b70c5981e5d0e01d.png)
进入安装
![进入安装](http://i13.tietuku.com/f3da45b66859b02c.png)![设置网页](http://i13.tietuku.com/02c2c404a7a5ff8d.png)![地区设置](http://i13.tietuku.com/5e75b575a4d1ab92.png)![还是设置](http://i13.tietuku.com/15ffc8c41ee6fea2.png)![币种设置、税设置](http://i13.tietuku.com/e8eb4384fa2714db.png)
后面的应该很清楚了，币种选人民币，税的事情，中国人很少在电商上面谈税，商店也没有谈税的，都算进去了。不过老美不一样，税都专门谈，尤其是他们不同的州税还不一样。![安装示例Store](http://i13.tietuku.com/90075575c7075d61.png)
为什么不选用翻译呢。因为翻译工作做的不是太好，有的是翻译的有的还没翻译，“单价、购物车”和“Address、Postal Code”混杂在一起，不伦不类。汉化的话还是最后一块弄吧，先学会了再说。
Importing Content这个过程比较慢。稍微需要等一等。
![开始](http://i13.tietuku.com/1855c0e37aa128a3.png)
这里就完成啦，感谢Commerce Guys。
但是这样的话，如果断了就没法上网了，怎么办。
最终的解决办法是：
@import url(//fonts.googleapis.com/css?family=Open+Sans:400,700,300,600,800);
@import url(//fonts.googleapis.com/css?family=Gentium+Basic:400,700);
把所有的文件里，含有这两个恶棍的东西全部删掉。效果斐然。
