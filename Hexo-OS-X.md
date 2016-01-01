title: 'Hexo在Mac OS X 10.9.X下的搭建'
date: 2014-05-31 14:48:06
categories: ['学习笔记']
tags: [Hexo, OS X]
---
这是第一篇在Hexo下写的博客。因为也想要使用静态技术来做一个简单的页面。事实证明，这种技术采用起来十分简单，尤其是在 Mac OS 的环境下，搭建几乎不会耗费太多精力。还是很值得尝试的。我现在对于MarkDown的技术格式还不是很了解，虽然据说应该还是超简单的说，应该很快就会学会吧。
废话不多说，现在就开始说刚才的十几分钟内，我做了一些什么。
##安装
首先是需要npm，因为Hexo技术是基于Node.js来做的。就像Jekyll是基于Ruby的一样。因此，在Hexo之前必须要安装npm。
在Mac下安装Node.js非常方便简单，只需要到[Node.js的官网](http://www.nodejs.org)上，点击一下Install按钮，就会自动下载到安装包，按照顺序点击下一步就可以了。现在最新版本是v0.10.28，我安装的是这个版本。

按照步骤安装了Node.js之后，就可以在Bash中使用npm命令了，下面键入：
``` bash
$ sudo npm install -g hexo
```
就可以下载好Hexo了。这个过程很快，输入密码，等一等，它就会自动从网上下载好包并进行安装。这个过程因为网络问题有可能失败，重做几次应该不会有问题，我是一次就成功了。
我安装的Hexo版本是2.6.1

<!--more-->
##建立工作目录
就像eclipse需要workspace一样，我们也该专门弄个目录来存放各种东西吧。这里我把他放在Documents文件夹下，我建立了一个新的文件夹，直接就叫做hexo。
``` bash
$ cd ~/Documents
$ mkdir hexo
$ hexo init
```
大功告成，这个时候，bash给了提示：
``` bash
[info] Copying data
[info] You are almost done! Don't forget to run `npm install` before start blogging with Hexo!
```
遵从指教，键入命令。

``` bash
$ npm install
```
又咕嘟咕嘟下载了一些东西，这一些不需要管。
输入新的生成命令，和运行服务器的命令
``` bash
$ hexo generate
$ hexo server
```
这个时候，下面就会提示，服务已经运行在了4000端口上，按下CTRL+C退出服务。这个时候我们用浏览器，输入"localhost:4000"，就可以看到那个界面啦！
界面中有一篇hello-world的文章，是用Markdown语法已经写好的，给写文章一点小的启迪和入门。

##来一篇新的博文
现在这个网站就已经可以在本地上访问了。我们可以采用下面的命令来添加博文。
``` bash
$ hexo new "my_new_post"
```
然后，在文件夹下，就会多出一个md文件，只需要修改这个文件就可以完成一篇新的博文了。
因为我们的网页是静态网页，所以每次修改了东西，都需要使用generate命令来从新生成一下。

##上gitcafe发布网页
我们建立这个网站当然不是为了自己在自己的电脑上看着输入，应该是需要上传到网络上，供别人看的。[github](http://www.github.com)是世界上最常用的git托管网站，github提供page服务，为用户免费托管静态页面。但是github是外国的服务器，在国内用不是太稳定，所以我们改用内地的，可以有相同功能的[gitcafe](http://gitcafe.com)来完成我们的发布。

首先在网站上建立一个用户，记得用户名不要太复杂。接下来配置SSH信息，这个网上有很多教程，我在这里就不细说了，应该搜索 SSH、git 就可以搜到一堆配置的指南。接下来创建一个项目，记得项目名要和用户名一致。
最后也是最重要的，在_config.yml中对相关的参数进行设置，比如设置自己网站的标题等等，尤其是最后的deploy。
我这里的设置是：
``` bash
deploy:
  type: github
  repository: https://gitcafe.com/marchon/marchon.git 
  branch: gitcafe-pages
```
记得，branch一定要是gitcafe-pages，这样才会应用page服务。
设置完成后，我们再generate一遍，调用
``` bash
$ hexo deploy
```
就可以了。注意，第一次要输入密码的，以后就不需要了。
