title: Mac下修改iTunes中iPhone的自动备份路径
date: 2015-10-25 14:22:52
categories: ['技术指南']
description: auto-backup for iPhone
tags: [OS X, iOS]
---

# 不废话，直入正题

## 我的配置

| Item          | Value         |
| ------------- |:-------------:|
| OS            | OS X El Capitan 10.11.1 |
| PC            | Macbook Pro 13' 8GB 256GB  |
| Portable Disk | WD My Passport 2TB |
| NTFS mounting | Tuxera NTFS for Mac       |

首先连接移动硬盘，那么它会被挂载到位置：

```bash
/Volumes/Ultra Passport
```

<!--more-->

为什么叫做 _Ultra Passport_，这只是我自己给我的移动硬盘起的卷名而已。它买来默认其实是My Passport。
然后，iPhone默认的备份路径是：

```bash
/Users/marchon/Library/Application Support/MobileSync/Backup
```

为什么叫做 _marchon_，这只是我自己的用户名而已。<img src="http://i11.tietuku.com/76baa07805638f0d.png"/>

## 配置方法

如果你以前没有重要的备份的话，那么就直接把/Users/marchon/Library/Application Support/MobileSync/这个文件夹删除。

然后运行把它连接到目标位置就可以了。

命令：

```bash
cd /Volumes/Ultra\ Passport/
mkdir MobileSync
cd /Users/marchon/Library/Application\ Support/
rm -rf MobileSync
ln -sf /Volumes/Ultra\ Passport/MobileSync MobileSync
```

注意，如果运行你自己的程序，要把我的 _Ultra Passport_ 换成你自己的路径。
另外不要忘了在空格前面加上'\'。
这时候就大功告成了，选择备份iPhone的时候，备份内容会自动备份到移动硬盘里。
64GB的iPhone，使用了50多GB，备份出来的内容实际上只有20GB，并没有想象中那么大。
另外大家如果不是那么急着每天备份的话，可以把“连接该手机时自动备份”的选项关了。否则，当你连上了手机但是忘了连移动硬盘的时候，它就会给你报备份错误。虽然这个错误无伤大雅，再次连接的时候依然可以正常备份。

# 磁盘格式那些事儿

使用iPhone自动备份当然是一件非常开心的事情，但是 Macbook 的硬盘也并不富裕。中配的13‘ Macbook Pro 也只是 256GB的SSD，在买一个中配的iPhone 64GB， 那么备份一次又要消耗多很多容量。
照片的自动备份也是一个很耗费容量的东西，尤其是对于摄影爱好者，存2000张RAW照片，那么40GB空间就没了。录几个4K视频，几十GB又没了。
然而 __照片__ 这个应用是可以更换默认地址的。只需要按住 _alt/option_ 键，然后点击 _照片_ 的图标就可以了。
![修改照片的默认图库地址](http://i13.tietuku.com/870bfbc91243cec1.png)

当然，如果想要转移出去的话，需要一个格式为__HFS+__的移动硬盘。

__照片__应用是可以，但是现在我还是没有发现iTunes有类似的功能，但是实际上也不难搞定。

## HFS+ (OSX Extended)

首先连接上你的移动硬盘，最好是格式化为__HFS+__格式。
什么是__HFS+__呢？其实HFS+就是OS X扩展格式。
![HFS+](http://i13.tietuku.com/f8970461edc5d97e.png)
这个格式是苹果自创的磁盘格式，和微软常用的NTFS是不兼容的。直接连到Windows的电脑上，也是读取不了的。不过苹果自己的系统默认就是这个格式。
当然，如果不是这个格式也没有关系。
OS X 和 Windows 之间通用的格式，有FAT32格式和exFAT格式。

## FAT32

FAT32格式是从很早的系统中传承过来的，兼容性强，但是致命的缺点是不支持单个4G以上的大小的文件。如果你想要存一个蓝光电影，那么久不要想FAT32了。

## exFAT

exFAT格式是对FAT的扩展，支持苹果和微软的系统，但是它只是一个折衷的方案，适合于闪存存储（U盘、SSD等是闪存存储），不那么适合于磁盘存储，而且据论坛的人们说不那么稳定，有的时候的热插拔会导致数据丢失，甚至盘区的崩溃、数据的丢失，所以也不是那么建议使用。

## NTFS

NTFS (New Technology File System)，是 WindowsNT 环境的文件系统。新技术文件系统是Windows NT家族（如，Windows 2000、Windows XP、Windows Vista、Windows 7和 windows 8.1）等的限制级专用的文件系统（操作系统所在的盘符的文件系统必须格式化为NTFS的文件系统，4096簇环境下）。NTFS取代了老式的FAT文件系统。
该文件系统的详细定义属于商业秘密 ，微软已经将其注册为知识产权产品。所以现有的支持产品，挂载到Unix/Linux机器上，有可能支持比较好，但是不能完全依赖，因为微软把NTFS的细节作为商业机密隐藏了，不能保证NTFS的100%安全操作。
OS X迄今(2015年10月25日)为止是无法原生支持 NTFS 的，连接上NTFS的磁盘以后，是只读的。如果需要写入，那么就需要额外的插件软件。现在的一些软件做得也不错，比如[Paragon](http://www.paragon-software.com/)公司的[NTFS For Mac](http://www.ntfsformac.cn) 、[Tuxera NTFS for Mac](http://www.tuxera.com/products/tuxera-ntfs-for-mac/)等等。
这些插件的缺点是：

- 收费
- 系统更新了，软件也有可能必须更新才能继续支持NTFS。



