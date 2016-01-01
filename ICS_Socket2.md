title: Socket的一些Guide2 - 结构补充
date: 2015-12-10 13:44:52
categories: ['学习笔记']
toc: ture
description: 套接字接口（Socket Interface）是一组用来创建网络应用的函数。本篇接续上一篇《Socket的一些Guide》，重点图示一些数据结构、函数之间的关系，关系图系原创。
tags: [ICS, C, Socket]
---

# 数据结构

## 地址结构

这些结构是有关于地址的数据结构。

- **Struct&nbsp; sockaddr_in**
用来存储 协议家族Protocal family IPv4的IP地址和端口号。

- **Struct&nbsp; sockaddr**
即SA
用来作为普遍的、一般的、用于存储地址的结构。
PS: 因为C是面向过程的语言所以没有继承的概念。如果用面向对象的思维来分析的话，可以认为sockaddr是sockaddr\_in的基类（父类），sockaddr\_in是子类，专用于互联网IPv4协议栈下的应用地址的存储。

- **Struct&nbsp; sockaddr_storage**
就是sockaddr_in

## 信息结构

- **Struct&nbsp; addrinfo**
getaddrinfo这个函数的作用是输入 主机名（或地址）+ 端口号（或服务） ，输出Socket信息(包含上述的sockaddr地址信息)。
Struct addrinfo **result 是存储得到的addrinfo的结构。

这个还是借助上次的图来说明：
![Result 链表结构](http://imglf.nosdn.127.net/img/MGpGUW9CdGlzcDVPWHF3ZE91M2RybHpHeHVLSzN3ZzlnREFwelZQejJoM1dhNkxtL3JPdjl3PT0.png)

# 函数之间的关系

首先还是上一个章节的Sockets Interface图，这也是Socket部分最重要的图。
![socket client-server 结构](http://imglf0.nosdn.127.net/img/MGpGUW9CdGlzcDVPWHF3ZE91M2RycG5CZVRCaWFHTHNVTjVzNFJGUVdlTzVvc0kySmRiWUhnPT0.png)

那么，所列出的这些函数之间有什么关系呢？他们读取了什么数据，又产生了什么数据呢？下面用两张图来进行说明。

![Client](http://imglf0.nosdn.127.net/img/MGpGUW9CdGlzcDUvOCs0cEl4djNTQi9rYnl4cmdUdE0wQjV5YmlwL1RQSHQ5UlVsNkhqSm93PT0.png?imageView&thumbnail=1680x0&quality=96&stripmeta=0&type=jpg%7Cwatermark&type=2&text=wqkgWmFjaGFyeSAvIG1hcmNob24ucGt1b3Mub3Jn&font=bXN5aA==&gravity=southwest&dissolve=30&fontsize=340&dx=16&dy=20&stripmeta=0)

![Server](http://imglf2.nosdn.127.net/img/MGpGUW9CdGlzcDUvOCs0cEl4djNTQ0F6TVE2UGxORzZEK3IwR1NVck9VRnAvakZWQ1Nyc2dnPT0.png?imageView&thumbnail=1680x0&quality=96&stripmeta=0&type=jpg%7Cwatermark&type=2&text=wqkgWmFjaGFyeSAvIG1hcmNob24ucGt1b3Mub3Jn&font=bXN5aA==&gravity=southwest&dissolve=30&fontsize=340&dx=16&dy=20&stripmeta=0)

> 图例：

>方框代表函数
>圆圈代表数据

>蓝色实方框代表主要函数
>浅蓝色虚方框代表析构or关闭函数

>绿色圈代表内部数据
>金色圈代表外部数据
>橙色圈代表几个重要的fd：File Descriptor