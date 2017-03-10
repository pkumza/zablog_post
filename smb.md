title: 'Mac连不上smb协议？'
date: 2016-06-1 14:48:06
categories: ['技术指南']
tags: [OS X]
---

smb(Server Message Block)协议对于常用NAS的人应该不会陌生。在办公室，家庭安装一个NAS作为存储终端，放置文件、资源、电影，是一件非常方便的事情，尤其是在当今的各路网盘都不是非常安全的情况下，私有的云存储就变得更加重要。

> NAS(Network Attached Storage:网络附属存储)按字面简单说就是连接在网络上，具备资料存储功能的装置，因此也称为"网络存储器"。它是一种专用数据存储服务器。它以数据为中心，将存储设备与服务器彻底分离，集中管理数据，从而释放带宽、提高性能、降低总拥有成本、保护投资。其成本远远低于使用服务器存储，而效率却远远高于后者。
                ——  360百科

那么什么是SMB协议呢？

> In computer networking, Server Message Block (SMB), one version of which was also known as Common Internet File System (CIFS, /ˈsɪfs/), operates as an application-layer network protocol mainly used for providing shared access to files, printers, and serial ports and miscellaneous communications between nodes on a network. It also provides an authenticated inter-process communication mechanism. Most usage of SMB involves computers running Microsoft Windows, where it was known as "Microsoft Windows Network" before the subsequent introduction of Active Directory. Corresponding Windows services are LAN Manager Server (for the server component) and LAN Manager Workstation (for the client component).
                 —— Wikipedia

它用起来也极其简单，一般来说，只要连上的相应的局域网，就可以直接在文件夹中访问。
譬如在Mac中，如图：
![Mac](http://imglf.nosdn.127.net/img/MGpGUW9CdGlzcDRGK0IyZzFLK0FnSFlwVnhtKyt6MUVVZHQ3NTRaMVhLRG01NTk2ZklGeTdRPT0.png?imageView&thumbnail=1680x0&quality=96&stripmeta=0&type=jpg%7Cwatermark&type=2&text=wqkgWmFjaGFyeSAvIG1hcmNob24ubG9mdGVyLmNvbQ==&font=bXN5aA==&gravity=southwest&dissolve=30&fontsize=340&dx=16&dy=20&stripmeta=0)

*nix都差不多，在Network下一定会有，用同样的方式访问就行了。
![Linux](http://imglf0.nosdn.127.net/img/MGpGUW9CdGlzcDRGK0IyZzFLK0FnSU52ZVdVeEhrMkFETmMwNC9PU3JMeWxId3lBWVRSTllnPT0.png?imageView&thumbnail=1680x0&quality=96&stripmeta=0&type=jpg%7Cwatermark&type=2&text=wqkgWmFjaGFyeSAvIG1hcmNob24ubG9mdGVyLmNvbQ==&font=bXN5aA==&gravity=southwest&dissolve=30&fontsize=340&dx=16&dy=20&stripmeta=0)

Win的环境下，打开“我的电脑”，smb协议支持项目也一定在那里。手头没有Win的电脑…盗个老图（也许会失效，不过好像不重要）
![Win](http://about.eserver.org/smb_4.jpg)

但是有一种可能，在Win和Linux都可以开心地访问smb协议的时候，mac却死活登陆不上去。即使连接正确、用户名正确、密码正确，但是依然连接不上。

这个时候，不要犹豫，直接找到NAS，看看日期是否正确？如果日期时间差距太大的话，mac的smb协议是无法进行的。

