title: 'Drupal Commerce 模块使用指南1：简介和安装'
date: 2014-06-11 22:53:48
categories: 技术指南
tags: [Drupal, Commerce]
---

#简介
关于Commerce模块，可以说是在Drupal的框架下最好的电子商务模块了。在Drupal6中，有Ubercart,不过到了Drupal7以后，Commerce看起来已经超过这个老前辈了。
然而，Commerce模块的中文介绍实在是太少了，一个详细的免费的教程更是难以找到。这里就参考官网，对整个过程进行一个系统的介绍吧。下面基本上就是对官网[User Guide](http://www.drupalcommerce.org/user-guide)的翻译了。

#使用指南
提示：本使用指南认为你已经对Drupal有一定一定的熟悉程度。找到一个合适的模块集和一系列为内容类型和用户实体所安装的设定非常重要。如果需要详细的内容，请查看[安装Drupal Commerce模块](http://www.drupalcommerce.org/user-guide/installing-drupal-commerce)

如果你有一些管理Drupal的经验的话，那么建立一个Drupal Commerce网站是一件非常简单的事情。在Drupal7的发行版上，使用导航来操作后台比以往任何时候都要简单，但是仍旧有很多东西要学习。对于Drupal的新用户来说，本文档可能不太适合你，所以我们建议你在安装设置Commerce模块之前，先学会使用一些其他的Drupal7模块。

为了建立一个新的Drupal Commerce网站，你将需要使用Drupal7的字段系统来建立一个自定义的内容类型，并且使用复杂的*视图Views*和*规则Rules*模块来进行高级的商店定制。

本用户使用指南的对应版本是Commerce 1.x。
#本文组织（目录）
1. 安装Drupal Commerce模块
2. 产品Products
3. 价格
4. 购物车
5. 结账过程
6. 付款
7. 管理订单
8. 客户档案
9. 运费
10. 税的设置
11. 示例
<!--more-->

#安装Drupal Commerce模块
有两种方法来安装Drupal Commerce模块。你可以从头开始，首先安装Drupal7，然后启用Commence模块和它的依赖模块。这个过程在你使用你的网页买东西之前，需要对产品的设置和产品的显示进行额外的设置。然而，这也给你提供了更大的空间，在一开始，就可以对项目的设定做精确的控制。

一个更简单的方法是使用一个Drupal Commerce的Drupal发行版，叫做[Commerce Kickstart](http://drupal.org/project/commerce_kickstart)。但是这个工程的一个主要目的是在Drupal的安装中就对系统的内容进行设置，以满足市场和商业模式的需求。

下面的部分提供了安装的方法：
[从头安装](http://www.drupalcommerce.org/user-guide/installing-scratch)
[使用Commerce Kickstart安装](http://www.drupalcommerce.org/user-guide/installing-commerce-kickstart-1x)
[使用Ubercart迁移](http://www.drupalcommerce.org/user-guide/migrating-ubercart-stores)
（PS：具体的安装我就不说了，反正我已经安装好了哈哈哈。安装过程比较简单，如果你会Drupal7的话。）
