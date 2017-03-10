title: 【软件学报】一种大规模的移动应用第三方库自动检测和分类方法
date: 2017-03-10 18:00:39
categories: ['技术指南']
tags: [Android, 第三方库, LibRadar]
---
突然想起来前一阵子[软件学报](http://www.jos.org.cn)上中了一篇和师兄合作的文章，原文地址在[原文PDF链接](http://www.jos.org.cn/ch/reader/create_pdf.aspx?file_no=5221&journal_id=jos)，[备用PDF链接](http://www.cnki.net/kcms/download.aspx?filename=mlUNWdkeEdFa0gERwB1Nnp0RWlTZ140bvMnTohDVUZHdZNTQiNXcvQTYqdUR4AjSWlXTxpGWlNWaJNUSRVjVENUVIRDevdGVvB3YUNHUNFDVJ1WY4UlYjxEaQFVMwZFOTN2da90SWlXOMBFS5EmZ3oVNv9GcrIVR&tablename=CAPJLAST&dflag=pdfdown)，[CNKI知网链接](http://www.cnki.net/kcms/detail/11.2560.TP.20170220.1514.030.html)。

中国科学院软件研究所版权所有。
<hr>

# 一种大规模的移动应用第三方库自动检测和分类方法
王浩宇 1, 郭耀 2, 马子昂 2, 陈向群 2
1(智能通信软件与多媒体北京市重点实验室, 北京邮电大学计算机学院,北京 100876)
2(高可信软件技术教育部重点实验室, 北京大学信息科学技术学院软件所, 北京 100871)


### 摘要:

移动应用中广泛使用第三方库来帮助开发和增强应用功能.很多关于移动应用分析以及访问控制的研究 工作,需要在分析之前对第三方库进行检测、过滤或者对其进行功能分类.当前大部分研究工作都使用白名单的方 式来检测第三方库或者对其功能进行分类.然而,通过白名单检测第三方库不完善且不准确,其原因包括:(1)第三方 库的种类和数量很大,(2)常见的代码混淆或者第三方库伪装等技术使得白名单方法不能准确的识别第三方库.本文 提出一种第三方库自动检测和分类方法,包括基于多级聚类技术准确识别第三方库,以及基于机器学习对第三方库 的功能进行准确分类.实验对超过 130,000 个 Android 应用进行分析,验证所提出方法的有效性.实验总共检测到 4,916 个不同的第三方库.在人工标记的数据集上,通过十折交叉验证,对第三方库分类的准确率达到 84.28%.将训练 好的分类器应用于全部 4,916 个检测到的第三方库,人工进行抽样验证的准确率达到 75%.

### 关键词:

Android;第三方库;广告库;移动应用;机器学习

### 中图法分类号:

TP311
