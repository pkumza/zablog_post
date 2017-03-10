title: 更换GPU
date: 2016-09-11 20:26:44
categories: ['技术指南']
tags:
description: 记录一下自己更换显卡的过程。
---

哈哈我承认我没有拍摄什么高质量的图像。记录一下更换显卡的过程，供参考。

主板和显卡之间的连接通道是PCI-E接口。
现在的主板越来越厉害了，所以可能有多个PCI-E接口，同时要有版本的区别，比如PCI-E v3/v2, x16/x8。当然要学会插对，这个可以参考[显卡插错插槽的后果?PCI-E X16与X8性能PK](http://diy.pconline.com.cn/graphics/reviews/1210/3047220_all.html)
一般来说问题不大，显卡和主板的兼容性是很好的，很少有说因为主板不支持而无法连接显卡的情况，除非那主板实在是过于过于过于古老了。

# 原机
台式机的型号是 2013年07月02日的 ThinkCentre M8400t，现在类似的版本应该已经升级到M8600t了吧。Core i7 3770 的CPU。
长得大概这个样子。
![](https://img.alicdn.com/imgextra/i1/318290277/T2fUmmXT0XXXXXXXXX_!!318290277.jpg)

# 购买

好啦，首先我在京东买了一个显卡，["索泰（ZOTAC）Geforce GTX1060-6GD5 X-GAMING OC 1569-1784MHz/8008MHz 6G/192bit GDDR5 PCI-E显卡"](http://item.jd.com/3281156.html)。

![](http://img20.360buyimg.com/vc/jfs/t2731/180/3429863697/149042/496970ed/578d99afN946f7efb.jpg)

GTX 1060 虽然没有呼吸灯，但是依然是2016年秋季最具性价比最值得购买的产品之一，性能和GTX980相当。

# 拆机箱

这部分没啥好说的，螺丝卸了就拆了，太熟练，没有坎儿。前几天拆下来原有的硬盘，换了一个2TB的硬盘，重新安装Win10系统，版本号10240，使用学校公共购买的激活了一下[北大良心软件](http://software.pku.edu.cn/)。

# 连电源

实现拆开瞅了一下，并没有给显卡供电的接口。提前做了一下知识，现在的主流显卡有 8pin/6pin 两种接口，就是8口线和6口线。
一般低端显卡，只需要主板供电即可。主板直插可以给显卡供给大概75W吧，这对于低端显卡已经足够了。
中端显卡一般是6pin的供电，比如GTX950什么的。
今年大概10月份英伟达应该会推出GTX1050，它也许就会使用6pin供电。
GTX1060的定位是中高端显卡，和GTX1080 GTX1070同属于高端系列。GTX1060这种显卡的供电需要就更大了，所以使用了8pin供电。
GTX1080这种高端显卡甚至需要8+8pin供电。

联想自带电源非常可怜，功率上还不错，可以提供360W的功率，但是在线材上就太可怜了，只提供了一个14pin和一个小4pin出来。这显然不能供显卡使用。

以前有些显卡是不接外接电源也能使用，但是效果比较差的。现在这些显卡没有外接电源的后果就是黑屏，没什么好说的。

于是买了一个电源来给显卡供电。["安钛克（Antec）额定450W VP 450P 电脑电源（双显卡接头/两年质保/主动式PFC/12CM静音风扇/台式机电源）"](http://item.jd.com/251340.html)

![](http://img20.360buyimg.com/vc/jfs/t1705/276/1215416282/100651/6e726fde/55e53779N387e8184.jpg)

现在终于有8pin的口了。PS：事实上是6+2pin。6+2pin的设置就是既支持6pin，又支持8pin。

VP-450p 这个电源支持双8pin的，不过我的小显卡不需要。如果买了1080或者双显卡，肯定就需要了吧。

不过现在难题来了。VP-450p只提供20+4pin，并不提供14pin的主板供电电源。这个好坑，只有联想等少数厂商才用这种无聊的14pin来给主板供电。我并没有别的办法啊。我异想天开地决定使用两个电源，即老电源供电主板，新电源供电显卡。不过无情的现实还是啪啪啪地打脸。没有主板向电源发送的“开机信号”，电源根本就不供电啊，所以显卡的灯不亮，风扇也连转都不转。

机器都拆开了，没办法装，这可咋办。好在依托了北大的位置优势。上某宝上迅速查找一个20转14pin的连接线。当然啦最后只找到了24转14pin的连接线，不过没关系，我的电源是20+4pin的，可以组合为24pin。询问商家，真巧，那个商家就在中关村。骑上单车十分钟就买了来，才花了RMB10。上图！

![](http://imglf2.nosdn.127.net/img/MGpGUW9CdGlzcDZXMU9GY2JtZHhmeG9EVTFKTmtzYWxqMytiemFKNUExdFJ3dDJ6b21sd2tRPT0.jpeg?imageView&thumbnail=1680x0&quality=96&stripmeta=0&type=jpg%7Cwatermark&type=2&text=wqkgWmFjaGFyeSAvIG1hcmNob24ubG9mdGVyLmNvbQ==&font=bXN5aA==&gravity=southwest&dissolve=30&fontsize=340&dx=16&dy=20&stripmeta=0)

大拇指端是24pin，右边是14pin。回来连接上，左手连接电源，右手连接主板，Works。

# 连接显卡

首先把旧的显卡拔下来，注意的是主板PCI-E接口都有一个卡口，要拨动一下才能够拔下显卡。新的显卡明显比原来的显卡要大，长了1.5倍，宽了1.4倍，厚了大概3倍。

![](http://imglf1.nosdn.127.net/img/MGpGUW9CdGlzcDZXMU9GY2JtZHhmL1N2N1lFellDR1FIQklqMFVnQzl3WlR0UmlsL3BvblJ3PT0.jpeg?imageView&thumbnail=1680x0&quality=96&stripmeta=0&type=jpg%7Cwatermark&type=2&text=wqkgWmFjaGFyeSAvIG1hcmNob24ubG9mdGVyLmNvbQ==&font=bXN5aA==&gravity=southwest&dissolve=30&fontsize=340&dx=16&dy=20&stripmeta=0)

令人失望的是，主板的结构实在是太坑了，所以因为体积占掉了两个SATA数据接口。SATA3给占掉了，只剩下SATA2，大概也许就没有升级SSD的任何必要了。

# 安装驱动

ZOTAC赠送的光盘大概也许没什么意义，除了一个FireStorm，可以用来调节风扇、频率、电压等等。

好不容易把最新的驱动安装程序下载下来，但是提示

>NVIDIA安装程序无法继续

>此NVIDIA驱动程序与此Windows版本不兼容。

![](http://imglf1.nosdn.127.net/img/MGpGUW9CdGlzcDZXMU9GY2JtZHhmemhUWVNJQnhOSXUzNEFodnVYRlk2ckhuTlRBeU81RE5RPT0.jpeg?imageView&thumbnail=1680x0&quality=96&stripmeta=0&type=jpg%7Cwatermark&type=2&text=wqkgWmFjaGFyeSAvIG1hcmNob24ubG9mdGVyLmNvbQ==&font=bXN5aA==&gravity=southwest&dissolve=30&fontsize=340&dx=16&dy=20&stripmeta=0)

明明是Windows10，明明是64位，明明是GTX1060，哪里就不兼容了呢？答案是这个驱动和10240不兼容。

话说，Win10在可预见的年限内不会有版本号的变更了，就是说没有Win11的计划了。但是这不代表Windows10没有更新。版本号还是在一直变更的，10240是最初的基础版本的版本号，后面有很多更新。距离Win10推出已经一年多了，势必有很大的更新。

我搞了一个微软Windows10易升，把Window升级到了版本1607，然后再次安装，就成功了。

效果就不晒了，和网上宣传的没有太大差别。撸大师显卡跑分可以超过97%的用户。

# 总结

做好调研再买东西。做好调研再买东西。做好调研再买东西。
