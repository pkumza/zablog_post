title: FBX SDK Sample Guide - for beginners
date: 2016-08-02 12:46:23
categories: ['技术指南']
tags: ['3D', 'FBX']
description: FBX是3D模型常用的文件格式，为了开发直接处理FBX的软件，AutoDesk公司提供了C/C++和Python的两套SDK。这里介绍一下FBX SDK Sample for C++的内容，顺序按照从易到难，而不是字母顺序。
---

Current Version: FBX SDK 2016.1.1 for VS2012

介绍一下FBX SDK Sample的内容，顺序按照从易到难，而不是字母顺序。

## UI Example
最简单的Example，UI Example，是直接运行之后就有图案的。
文件夹下有三个例子，分别是：

- CubeCreator
- ImportExport
- SceneTreeView

首先看的是ImportExport，这个最基础。因为使用FBX SDK的最基础的内容，无非是Import和Export。

使用方法很简单，首先打开VS2012，然后点击FILE-Open-Project/Solution，然后再文件浏览器中选择C:\Program Files\Autodesk\FBX\FBX SDK\2016.1.1\samples\UI Examples\ImportExport\ImportExport_net2012.vcxproj即可。不用修改任何东西，点击运行，就会出现如下界面。

![ImportExport1](http://imglf.nosdn.127.net/img/MGpGUW9CdGlzcDdWYkNFM1BkbE5FQ0FPOEErYzhwN25maXc0d1RnV3JTaXpTZ2RmWkFoTzJnPT0.png?imageView&thumbnail=1680x0&quality=96&stripmeta=0&type=jpg%7Cwatermark&type=2&text=wqkgWmFjaGFyeSAvIG1hcmNob24ubG9mdGVyLmNvbQ==&font=bXN5aA==&gravity=southwest&dissolve=30&fontsize=340&dx=16&dy=20&stripmeta=0)

执行。

![ImportExport2](http://imglf2.nosdn.127.net/img/MGpGUW9CdGlzcDdWYkNFM1BkbE5FT05WbXBMQlFmRWZDaGdHZXV4K1NnSnQ3cHBqZk0yczlBPT0.png?imageView&thumbnail=1680x0&quality=96&stripmeta=0&type=jpg%7Cwatermark&type=2&text=wqkgWmFjaGFyeSAvIG1hcmNob24ubG9mdGVyLmNvbQ==&font=bXN5aA==&gravity=southwest&dissolve=30&fontsize=340&dx=16&dy=20&stripmeta=0)

没错就是这么简单，具体的功能就是导入然后直接导出而已，没有什么特别的。在我这个例子中，我导入了一个叫做cubes.fbx的文件，然后导出了cube.1.1.fbx文件。

## SceneTreeView

下面的例子我就不去一一运行了，因为确实是很简单。

SceneTreeView这个例子，是打开一个FBX，然后把这个FBX的所有内容按照树形图的方式展示。当然展示的主要内容是Node，包含着mesh、Attribute、Skin、Skeleton、Animation等等…

## CubeCreator

不断地创建Cube。从这个例子可以好好地学习更基础的FBX中的模型的数据结构。再有就是映射方式，Direct和IndextoDirect的区别一定要掌握。

## Instance

/****************************************************************************/
/* This example shows how to instanciate meshes and curves in FBX.          */
/*  - Create a cube                                                         */
/*  - Create instances of this cube (new nodes that point to the same mesh) */
/*  - Apply the same materials to the polygons of these cubes               */
/*  - Create an animation curve                                             */
/*  - Animate all cubes using the same animation curve.                     */
/****************************************************************************/

创建一个立方体
创建立方体的多个实例
为这些立方体的面应用同样的材质
创建一个动画轨迹
为所有的立方体应用同样的动画轨迹

#### 实例化

为了节约模型的空间，有的模型在一个场景中需要出现多次的。比如草地上的草，桌子上的杯子，赛场的座位等等。这些模型没有必要做那么多，否则很耗费文件大小。这个时候一样的东西只要定义一次，然后挂载到不同的Node上就可以了，这个过程称之为实例化。

## UserProperties

用户自定义的内容。

/////////////////////////////////////////////////////////////////////////
//
// In this example a scene is created containing a cube and a pyramid.
//
// The example illustrates two things:
//  1) How to create user properties, attach them to the cube and
//     animate them.
//  2) How to create a constraint, constraining the pyramid to the cube.
//
//
/////////////////////////////////////////////////////////////////////////

在这个例子中，首先创建了一个含有一个立方体和一个金字塔的场景。
这个例子展示了两个功能：
1） 如何创建用户内容，然后把内容附到cube上。
2） 如何创建一个约束，使金字塔约束到立方体上。
