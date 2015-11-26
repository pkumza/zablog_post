title: '[Android Cocos2dx搭建]Mac电脑OS X下搭建Cocos2d-x 3.2的Android环境2014年8月最新版本'
date: 2014-08-26 12:16:52
categories: 学习笔记
description: 在Mac电脑中，搭建Xcode、Cocos2dx、Android的开发环境，2014年8月最新版。
tags: [OS X, Cocos2dx, Xcode, Android]
---
##下载Cocos2d-x
下载地址
[http://cn.cocos2d-x.org/download/](http://cn.cocos2d-x.org/download/)
当前最新版本是v3.2
解压到合适的位置，这里我解压到了/Applications/文件夹下，路径为
```bash
/Applications/cocos2d-x-3.2
```

<!--more-->

##下载SDK
建议到Android官方地址下载ADT Bundle for Mac，这样就省去下载Eclipse和配置SDK的时间了。
下载地址[http://developer.android.com/sdk/index.html](http://developer.android.com/sdk/index.html)
我下载的内容是adt-bundle-mac-x86_64-20140702.zip
下载地址是[http://dl.google.com/android/adt/adt-bundle-mac-x86_64-20140702.zip](http://dl.google.com/android/adt/adt-bundle-mac-x86_64-20140702.zip)
##下载NDK
下载地址是[http://developer.android.com/tools/sdk/ndk/index.html](http://developer.android.com/tools/sdk/ndk/index.html)
在尝试中，我首先下载了ndk版本为r9c，但是出现错误。错误后面会说明
后来我尝试下载了最新版的ndk64，版本为r10，但也出现了错误。错误后面会说明
保险起见，最后我下载了ndk r9d，发现可以使用。
把NDK解压到合适的位置，这里我解压到了/Applications/adt/文件夹下，路径为：
```bash
/Applications/adt/android-ndk-r9d
```
##下载JDK
首先检测JDK是否安装，打开命令行，输入
```bash
java -version
```
如果java命令可用，就说明已经下载好了java。我的提示信息如下：
```bash
java version "1.8.0_05"
Java(TM) SE Runtime Environment (build 1.8.0_05-b13)
Java HotSpot(TM) 64-Bit Server VM (build 25.5-b02, mixed mode)
```
这说明我已经安装了1.8.0_05版本的JDK
##解压ADT-Bundle
把之前下载的ADT-bundle的zip文件解压到适当的位置，我解压到了/Applications文件夹。路径为
```bash
/Applications/adt-bundle-mac-x86_64-20140702
```
打开adt-bundle下的eclipse文件夹，打开eclipse，尝试创立一个Hello World的Android程序。如果成功，说明Eclipse已经准备就绪。
##配置NDK环境
```bash
sudo vim /etc/profile
```
在下面加上这一行
```bash
export NDK_ROOT = "/Applications/adt/android-ndk-r9d"
```
重启终端才能生效。
##创建HelloWorld项目
进入Cocos2dx的文件夹，并调用cocos命令。
```
cd /Applications/cocos2d-x-3.2/tools/cocos2d-console/bin/
cocos new HelloWorld\_for\_Android -p com.maziang.hi -l cpp -d ~/Desktop/Work
```
这样就建立了一个项目
##编译为Android项目文件
执行proj.android目录下的build_native.py命令
使用ndk版本为r9c的时候，出现如下错误:

```bash
$ ./build_native.py 
The Selected NDK toolchain version was 4.8 !
Android NDK: WARNING: APP_PLATFORM android-19 is larger than android:minSdkVersion 9 in ./AndroidManifest.xml    
Android NDK: Invalid APP_STL value: c++_static    
Android NDK: Please use one of the following instead:  system stlport_static stlport_static_hard stlport_shared stlport_shared_hard gnustl_static gnustl_shared gabi++_static gabi++_shared libc++_static libc++_shared none    
/Applications/adt/android-ndk-r9c/build/core/add-application.mk:274: *** Android NDK: Aborting    .  Stop.
Traceback (most recent call last):
  File "./build_native.py", line 159, in <module>
    build(opts.ndk_build_param,opts.android_platform,opts.build_mode)
  File "./build_native.py", line 146, in build
    do_build(cocos_root, ndk_root, app_android_root,ndk_build_param,sdk_root,android_platform,build_mode)
  File "./build_native.py", line 82, in do_build
    raise Exception("Build dynamic library for project [ " + app_android_root + " ] fails!")
Exception: Build dynamic library for project [ /Users/marchon/Desktop/plter/Hello_Android_From_Cocos2dx/proj.android ] fails!
```

当使用64位ndk，版本r10的时候，出现如下错误：

```bash
$ ./build_native.py 
Couldn't find the gcc toolchain.
``` 

当使用ndk版本为r9d的时候，成功编译。
第一次编译时间会比较久，以后就会好一些。
事实上，并非r10不支持Cocos2d-x，而是因为我下载r10的时候下载了64位的版本。
这是因为*【Android L】*推出以后，Android设备要开始进入64位时代，所以推出了64位的ndk。但是Cocos2d-x暂时还没有支持64位的ndk，因此下载64位的ndk，就会出现错误。
ndk的32、64位和Mac操作系统或者Windows操作系统的32、64位又不是一回事，所以现在ndk的下载页面，有12个选项可以下载，分别是
| 32位NDK | 32位Windows |
| 32位NDK | 64位Windows |
| 32位NDK | 32位OS X    |
| 32位NDK | 64位OS X    |
| 32位NDK | 32位Linux   |
| 32位NDK | 64位Linux   |
| 64位NDK | 32位Windows |
| 64位NDK | 64位Windows |
| 64位NDK | 32位OS X    |
| 64位NDK | 64位OS X    |
| 64位NDK | 32位Linux   |
| 64位NDK | 64位Linux   | 
这里，我重新下载了32位NDK 64位OS X的包，下载后解压，并设置/etc/profile，和r9d一样，也可以成功运行。
```bash
export NDK_ROOT = "/Applications/adt/android-ndk-r10"
```
注意这个android-ndk-r10和64位版本是重名的，因此我提前把64位的没法使用的ndk更名位android-ndk-r10-64了。
现在这个r10的ndk虽然不是64位的，但是至少也是最高的版本，自此，本教程所有的软件、内容、开发工具都是最新的版本了。
不过依然不建议使用r10的版本，尤其是看到这个问题之后：
[http://cn.cocos2d-x.org/tutorial/show?id=1187](http://cn.cocos2d-x.org/tutorial/show?id=1187)
使用r10版本会出现如下错误：
```bash
/Users/minggo/SourceCode/cocos2d-x/build/../cocos/./3d/CCBundleReader.cpp:94:23: error: 
  return type of out-of-line definition of 'cocos2d::BundleReader::tell'
  differs from that in the declaration
ssize_t BundleReader::tell()
  ^
/Users/minggo/SourceCode/cocos2d-x/build/../cocos/./3d/CCBundleReader.h:90:14: note: 
  previous declaration is here
long int tell();
```
该链接错误解决起来比较困难，已经超出了普通的Cocos2D-x的学习范畴，所以在Cocos2D-x官方给出解决方案以前，不要使用r10的NDK。
（PS:正式版中最新，2014年8月25日。）
##导入项目到Eclipse
在Eclipse中，选择File->import，然后导入Android程序Existing Android Code Into Workspace
导入成功之后会有错误提示，这个时候还需要把一个lib文件夹导入到里面。
最新版和旧版路径不一样，在3.2版本中，需要把Path\_to\_your\_demo/cocos2d/cocos/platform/android/java/src文件夹下的org文件夹导入进来，和已有的代码文件并列。
这个时候，点击运行，再选择设备，就可以成功地在Android手机上运行Cocos2dx的程序了。

