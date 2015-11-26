title: 'Drupal Theme 主题系统'
date: 2014-06-22 13:48:48
categories: 技术指南
tags: [Drupal]
description: Drupal的外观控制是最为灵活可定制的，但是这个系统复杂而且不够直观。本文会对Theme系统进行介绍。
---
Drupal的外观控制是最为灵活可定制的，但是这个系统复杂而且不够直观。另外，某些思维不够流畅的作者经常会写出一些晦涩难懂的*Drupal主题高级特性*之类的文章，把学习Theme的激情完全打垮了。为此本文会从一个相对简单的视角，对Theme系统的原理、分布和一些使用做出介绍。
#工作原理
Drupal终归还是一个基于PHP的CMS，所有的最终技术还是通过HTML、CSS、PHP这些来完成，而那些布局和效果主要还是通过HTML和CSS和协作来完成。如果对于HTML和CSS不够了解，那还是先学习这些方面为好。尤其是现在HTML5和CSS3已经很成熟，能够方便、高效率地显示出优秀的效果。
另外，Drupal是一个CMS，而CMS的重要特征就是MVC式的开发，让显示和数据、控制分开。Drupal在这方面做的很好，甚至把View部分更加细化，数据的具体显示和布局，整体的显示和布局甚至都进行分开，使得Theme更专心于整体的显示效果。
Drupal采用了PHPTemplate这个主题引擎，来把这些显示技术结合起来。这个引擎在Drupal的核心中工作，操作Drupal的时候，改核心Core是大忌，一般不应该被修改，否则不仅会使得系统不稳定，而且破坏了开发的模块性，使开发陷入一团糟。我们需要做的就是使用模块化的方式，单独使用一些显示技术来实现对Drupal主题的定制。
#实现概览
Drupal中，有很多默认的主题。
在Drupal6的阶段，默认的主题一般是Garland主题，这是一个蓝色的，常见的主题。在Drupal7中，默认主题变为了Bartik，我觉得这个主题确实是比Garland要更好看一些。另外，系统默认还会安装一些其他的主题，比如seven、stark等等。
主题的定制可以简单，可以复杂。下面先说一下最简单的小定制。
<!--more-->
##最简单的小定制
这里以定制默认主题Bartik为例。运行环境为Drupal7原版。
登陆管理页面，管理-》外观-》Bartik设置
![Theme 1](http://i11.tietuku.com/b1208924f93f9473.png)
这时就进入了主题设置页面。
![Theme 2](http://i11.tietuku.com/996b1791487fd4e1.png)
在这里，可以给网页的配色进行设置，更改标志logo，更改favicon。
具体操作就不说了，简单到爆。如果没有额外需求，我觉得用Bartik加上简单的颜色配置、logo配置就足够了。
除了颜色和logo之外，主题系统还提供了更多的配置，可以做一些常用的定制。
主题系统可以对以下功能进行开关：
- 站点图标
- 站点名称
- 站点口号
- 文章中显示用户头像
- 评论中作者头像
- 评论中的用户验证状态信息
- 快捷图标
- 主菜单
- 次级菜单

当然，如果不进行自定义的话，细节是没法修改（一般也不用修改），布局没法修改。这些都是定制好的，直接使用就可以了，简单方便。
如果需求简单，那么文章看到这里就行了。如果需要更深层次的定制，继续往下看。
##主题存储路径
在Drupal的安装目录下，有一个themes文件夹。
路径：
```Python
/themes
```
文件夹下面一般有几个默认主题：
![Theme 3](http://i11.tietuku.com/8747745176a0b1e5.png)
我的这个文件夹有四个主题，分别是Bartik、garland、seven、stark。
README.txt对这个文件夹的内容做了说明，
engines不是主题，它里面是PHPTemplate主题引擎。
如果向Drupal中添加新的主题，不应该放在默认文件夹里，而应该放在
```Python
/sites/all/themes
```
目录下。
这不仅可以和核心主题做区分，还可以对所有的站点可用。如果采用Drupal来支持很多站点的话，这是必需的。
上面展示了两个路径。但如果你使用的是定制的Drupal的话，还有可能在别的路径下面。比如你使用了**Commerce Kickstart**，那么下面这一条路径放置着Kickstart默认的主题：
```Python
/profile/commerce_kickstart/themes
```
##目录结构
下面是Drupal7的例子。可能和Drupal6有一些不一样，时代更新了还是以Drupal7为准吧，等Drupal8出来再说别的。
打开一个主题看下面有什么，以Barktik为例：
![Theme 4](http://i11.tietuku.com/0804d12462cc96c1.png)
- .info——该主题的信息文件，包含着主题的必要信息。比如主题名称、主题的版本、主题对应的核心版本等等。
- color——包含主题配置管理器中的Color模块功能所需要的文件
- css——包含了主题特有的CSS文件
- images——包含了Bartik主题使用的图像
- logo.png——Bartik主题的logo
- screenshot.png——主题预览
- template.php——模板php
- templates——包含了Bartik主题的模版文件

下面介绍模板文件。
基于PHPTemplate的主题都包含一个或者多个模版文件。他们的扩展名是tpl.php。
模版文件为页面提供了布局框架。Drupal系统中有一些默认的模板。当主题不含有相应的模版文件的时候，将会采用默认模板。所以当一些内容不需要定制的时候，很多模版文件并不是必需的。与之类似的还有CSS文件，系统中也有很多默认的CSS。当用户自定义CSS的时候，这个CSS文件的优先级要高于内核的CSS。
在主题模板文件中，最核心的还是page.tpl.php。它是主题中最关键的文件之一。
同时，在CSS文件夹中，最核心的是style.css。
在Bartik中，有一个文件叫做teplate.php。这个文件看似是一个最重要的模板，因为它出现在了Bartik文件夹下，而且一看名字就很重要。但是实际上，打开这个文件并没有看到预想中很多的div，而是一些代码。这个文件主要就是包含很多的高级代码片段，是一些主题化函数的重写。这个文件并不是所有主题必需的。
#添加主题
这个部分也是一个相对来说非常简单的部分。
点击[https://www.drupal.org/project/project_theme](https://www.drupal.org/project/project_theme)就可以看到一大堆主题。
复制某个主题的下载链接，然后打开Drupal的管理页面，外观-》安装新主题-》从URL安装，就可以直接获得了。
或者，从网上下载到本地，然后解压到前面所说的这个文件夹内。
```Python
/sites/all/themes
```
然后再打开Drupal的管理员设置，设置一下外观，就发现该主题了。首先启用（Enable），然后设为默认（set default）就大功告成了。
不仅在Drupal官网上，还有很多网站也提供Drupal的主题。收费的主题一般都更加精美，而且小众化，也可以进行考虑。
常见的网站有：
- Acquia.com
- Dream Template
- FusionDrupalThemes.com
- Theme Garden

#定制主题
仅仅根据主题提供的选项进行配置，毕竟可以做的还是有限的。如截图所见，Bartik这种已经算是有较多配置选项的主题了，还可以定制颜色。很多主题的配置少得可怜，很多功能不能通过配置来实现。
这个时候就需要对已有的主题进行定制，或者创建我们自己的主题。
创建自己的主题还是算了，因为很多很多东西都要从头开始写。自己不过是想加一个功能而已，为啥要做那么多的事情，不划算。所以，子主题是最好的选择。
为什么不在原有的主题上直接进行修改呢。这就是模块化，怕弄乱了。前面也说了，之所以弄了两个文件夹，本身就是为了区分核心主题和自定义主题。
下面有一长串步骤，看起来很吓人其实也不难。现在我们要对Bartik主题进行一个定制，按照这个步骤进行操作就可以了。
1. 访问/theme目录，把bartik文件夹整个儿复制到/sites/all/themes目录下，更名为bartik2。
2. 把bartik.info文件同样改名，改为bartik2.info。
3. 打开bartik2.info进行编辑.
4. 把name从Bartik改为Bartik2，如果有需要可以更改Description描述。
5. 删除package、version两行。删除所有的stylesheets描述。
6. 添加新行：base theme = bartik。
7. 如果有自定义css的需要，可以在css文件夹中创建bartik2.css文件，并在bartik2.info中添加新行：stylesheets[all][]=css/bartik2.css。
8. 保存并关闭bartik2.info文件
9. 删除/bartik2文件夹中，不需要更改的文件。我第一次没有删除造成网站白屏，不知道为什么。后来吧template.php删除了就好了。可能是这个原因吧。反正这个template.php你应该是不需要修改的吧，一定要删除。
10. 搜索替换，把/bartik2文件夹中，除了bartik2.info意外，其他所有出现bartik字样的文件，把其中的bartik一律替换为bartik2。
11. 此时，打开网站，进入外观，就会发现Bartik2已经出现在了禁用主题的列表里，把它启用并设为默认，就可以了。
12. 这个时候会发现内容和Bartik完全一样。不过，修改Bartik2中的文件，就可以对主题进行定制了。

为什么要删除template.php，后来经过查相关资料，知道template.php是定义相关的规则。比如它可以控制加载哪些css，略过哪些css等等。如果保留这个文件，可能造成错乱，因此在第10步中，应该删除。如果对于相关东西没有研究，还是不要修改为好。
##定制CSS
CSS样式表是最常用的网页样式配置文件。最简单的方法就是直接在主题内部对CSS进行修改。

![Drupal Bartik原始标题](http://i11.tietuku.com/b1927b0e05ad9fdf.png)
上面这张照片是我们最常见的Bartik默认主题下的标题和网站口号。假如我们觉得这个标题字体太小了，想放大一点怎么办呢？首先在网页点击右键，然后检查元素（Safari的开发模式，Firefox，Chrome等众多浏览器都有这个模式的）。这是就跳转到了HTML源代码窗口。我们找到相关代码：
```HTML
<div id="name-and-slogan">
	<h1 id="site-name">
		<a href="/drupal/" title="首页" rel="home">
			<span>UI_Tech</span>
		</a>
	</h1>
	<div id="site-slogan"> 为梦想而生 </div>
</div> <!-- /#name-and-slogan -->
```
通过这一段代码我们可以发现，网站标题的id是"site-name"。我们要定位到"site-name"，来对网站标题进行修改。我们在css/style.css中，搜索"site-name"，发现附近的代码如下：
```CSS
#site-name {
  font-size: 1.821em;
  color: #686868;
  line-height: 1;
}
h1#site-name {
  margin: 0;
}
#site-name a {
  font-weight: normal;
}
```
我们把这一段代码复制到新建的bartik2.css中，然后把font-size修改为2.821.em。回到浏览器，强制刷新本页，就会发现，标题字体明显变大了。两个图的缩放比例是一致的，logo图片都是一样大的，只不过标题字体变大了。

![Drupal Bartik放大后的标题](http://i13.tietuku.com/b0c1dd90f180cd62.png)
是不是很简单呢？通过这种方法，页面上有任何觉得不符合心目中要求的，都可以通过这种方法。
只要你对CSS有一定的熟悉度，知道margin、fonts、size、color等设置选项，就可以对网页进行尽情地修改。
而且，这些修改完全不会破坏原有主题的完整性，不需要对系统主题做任何修改。因为本目录下的bartik2.css的优先级是要高于bartik主题中的style.css的。
当然，尽管优先级高，也不要忘记样式表重叠。当某个样式被应用的时候，系统会汇总样式表，合并选择属性。如果核心提供了某个选择器的属性，且主题没有设置与之矛盾的选择器，那么即使其他属性被重写，系统也会定义一个包含全部属性的选择器。
##定制PHP模板
上面说了，在靠后的步骤中，要删除不需要修改的部分。其实不删除也行，反正都是一样的，只不过因为重叠地太多，再区分优先级什么的，可能会变慢。
刚才，我保留了bartik2/templates文件夹，保留了文件夹下的page.tpl.php文件。其实不保留也没关系，再从bartik那里复制过来就可以了。
这个page.tpl.php文件，在上面也说了，是核心的模版文件。
在这个文件的上方有一段很长很长的注释，基本介绍了这个文件是做什么的。阅读一下这个注释对于看懂下面的内容非常有帮助。下面是开头的节选
```PHP
<?php

/**
 * @file
 * bartik2's theme implementation to display a single Drupal page.
 *
 * The doctype, html, head and body tags are not in this template. Instead they
 * can be found in the html.tpl.php template normally located in the
 * modules/system directory.
 */
 
```
翻译过来就是：
bartik2（之所以有2，是在前面的步骤中，把所有的Bartik都替换成了Bartik2）是一个用来展示单个页面的主题模板。Doctype、html、head、body这几个标签不在本模板中。作为替代，这些模板在html.tpl.php这个文件中。通常这个文件位于modules/system文件夹。
这个文件接下来定义了可以使用的全局变量，如$base\_path、$directory、$is\_front等等。
然后定义了Regions区域。
接下来就进入到了html标签，这里举例前几行：
```HTML
<div id="page-wrapper"><div id="page">

  <div id="header" class="<?php print $secondary_menu ? 'with-secondary-menu': 'without-secondary-menu'; ?>"><div class="section clearfix">

    <?php if ($logo): ?>
      <a href="<?php print $front_page; ?>" title="<?php print t('Home'); ?>" rel="home" id="logo">
        <img src="<?php print $logo; ?>" alt="<?php print t('Home'); ?>" />
      </a>
    <?php endif; ?>

    <?php if ($site_name || $site_slogan): ?>
      <div id="name-and-slogan"<?php if ($hide_site_name && $hide_site_slogan) { print ' class="element-invisible"'; } ?>>

        <?php if ($site_name): ?>
          <?php if ($title): ?>
            <div id="site-name"<?php if ($hide_site_name) { print ' class="element-invisible"'; } ?>>
              <strong>
                <a href="<?php print $front_page; ?>" title="<?php print t('Home'); ?>" rel="home"><span><?php print $site_name; ?></span></a>
              </strong>
            </div>
          <?php else: /* Use h1 when the content title is empty */ ?>
            <h1 id="site-name"<?php if ($hide_site_name) { print ' class="element-invisible"'; } ?>>
              <a href="<?php print $front_page; ?>" title="<?php print t('Home'); ?>" rel="home"><span><?php print $site_name; ?></span></a>
            </h1>
          <?php endif; ?>
        <?php endif; ?>

        <?php if ($site_slogan): ?>
          <div id="site-slogan"<?php if ($hide_site_slogan) { print ' class="element-invisible"'; } ?>>
            <?php print $site_slogan; ?>
          </div>
        <?php endif; ?>

      </div> <!-- /#name-and-slogan -->
```
这是从开头，一直到name和slogan结束。
这里我们进行一些简单的尝试。找到logo的图片相关代码，如下：
```HTML
    <?php if ($logo): ?>
      <a href="<?php print $front_page; ?>" title="<?php print t('Home'); ?>" rel="myhome" id="logo">
        <img src="<?php print $logo; ?>" alt="<?php print t('Home'); ?>" />
      </a>
    <?php endif; ?>
```
注意到，我已经把rel的值由“home”修改为了“myhome”。
这个时候，需要“清除主题注册表”，才能看到效果，这是必要步骤。
什么是“清除主题注册表”呢，就是清缓存啦。
有三种方法
1. 如果长期进行类似开发，可以安装Devel模块；
2. 如果超牛，对Drupal内核有很强理解，可以使用函数drupal\_rebuild\_theme\_registry
3. 如果就想试试效果，点击配置-》开发-》性能-》清除所有缓存，就可以了。

完成这些步骤之后，再打开网页，检查元素或者查看源代码，就可以发现源代码变成了这样：
```HTML
	<a href="/drupal/" title="首页" rel="myhome" id="logo">
		<img src="http://localhost/drupal/sites/all/themes/bartik2/logo.png" alt="首页" />
	</a>
```
虽然从外观上看起来没有什么变化，但是我确实从源代码上修改了模板文件。既然源代码都可以改，想要修改显示效果，还有什么不可以的呢？
