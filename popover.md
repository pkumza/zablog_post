title: Bootstrap Popover 点击外围区域自动关闭弹框
date: 2015-10-25 14:22:52
categories: ['技术指南']
description: Popover是Bootstrap中的一个弹框小工具，它方便易用，只需要在网页中插入一点代码就可以。但是它的实现也比较机械，选项颇少，如果想要定制效果还比较困难，需要自己进修改。这里我就添加了一些代码，让Popover更加亲民。
tags: [Bootstrap]
---

# 效果图

![满血Popover效果图](http://i11.tietuku.com/11c3495f71995c33.gif)

因为最近要做LibRadar的网页展示，所以需要表格来展示数据。但是对于每一个表项，它的Permission时长时短，没有办法在表格里显示，所以制作成了一个按钮，希望点击就可以出来一个弹出框，显示详细内容。
我的网页是使用[Bootstrap](http://bootcss.com)做的，所以可以方便地借助bootstrap的popover控件。

<!--more-->

# 基础Popover使用方法

首先把需要Popover效果的按钮加上class pop，再在属性里设置好data-original-title和data-content的内容。
举个例子如下：
```html
<button rel="popover" data-content="弹出框内容" data-original-title="弹出框标题" class="pop">Click me</button>
```


然后在外围用jQuery调用：

```javascript
$(document).ready(
    function(){
        $(".pop").popover();
    }
);
```

PS：_需要注意的是，js要首先引入jQuery，然后引入Bootstrap，最后引入这段有关popover的代码才行。_

不需要很多的设置，就可以实现最简单的弹出框的功能。
当然啦，我需要一定程度的自定义，所以我把这个函数变得更加复杂了。

```javascript
$(".pop").popover({placement:'left', delay: {show: 100, hide: 100}, html: true,
                  title: function () {
                    return $("#data-original-title").html();
                  },
                  content: function () {
                    return $("#data-content").html();
                  }});
```

这是我的LibRadar使用弹出框的样子：
![弹出框Popover](http://i11.tietuku.com/3813c8336737b71c.png)

## 解释一下：

- placement默认是right，就是弹出框出现在按钮的哪个方向。我这里强制改为左边了。
- delay是显示时间。我觉得直接瞬间显示出来太丑，可以顿0.1秒再显示。show和hide都是。
- html默认是false，这个时候title和content将都会以文本的形式显示。这里是变为了以html的方式显示，这样的话我就可以对显示效果进行控制和设置。（本来是想要加入close按钮的，但是后来失败了，html也就没有改回去。）


# 同学们注意了，我要开始__变形__了！


默认这么写，是可以实现popover弹出框，但是问题在于，点击按钮打开后，必须要点击同一个按钮才能关闭这个弹框。这让我感到很苦恼。而且popover的自定义能力比较弱，你没有办法把自己的控制代码写到里面去，所以需要进行自定义。
我在网上阅读了别人所做的例子【[例子1](http://www.cnblogs.com/tyhmj/p/3288551.html)】【[例子2](http://blog.csdn.net/scrawlor/article/details/17224839)]】，觉得对于我的代码并不能完全适用。因此，我做了自己的自定义，感觉应该算是挺好用的。我称之为 _满血Popover_。

```javascript
$(document).ready(
    function(){
        $(".pop").popover({placement:'left', trigger:'manual', delay: {show: 100, hide: 100}, html: true,
            title: function () {
                return $("#data-original-title").html();
            },
            content: function () {
              return $("#data-content").html(); // 把content变成html
            }});
        $('body').click(function (event) {
            var target = $(event.target);       // 判断自己当前点击的内容
            if (!target.hasClass('popover')
                    && !target.hasClass('pop')
                    && !target.hasClass('popover-content')
                    && !target.hasClass('popover-title')
                    && !target.hasClass('arrow')) {
                $('.pop').popover('hide');      // 当点击body的非弹出框相关的内容的时候，关闭所有popover
            }
        });
        $(".pop").click(function (event) {
            $('.pop').popover('hide');          // 当点击一个按钮的时候把其他的所有内容先关闭。
            $(this).popover('toggle');          // 然后只把自己打开。
        });
    }
);
```

### 解释：

1. popover函数里面进行了一点改动，就是多了一个trigger:'manual'，这个就给了自己很强的自定义空间。这是因为，我在之前的代码测试里面，trigger是默认的click，然后调用全局的 $('.pop').popover('hide'); 以后，再点击按钮，第一次是失效的。我觉得这应该是Bootstrap的Popover的一个Bug。使用自定义就解决了这个问题
2. 然后是body的click。在点击其他地方（不包括按钮本身、弹出框的内容）的时候，要全部hide。
2. $(".pop").click 就是因为，增加了manual之后，所有的hide和show就要通过自己写了。本来只需要toggle就可以了，为什么需要整体hide一下才可以呢？这是因为点开一个弹出框之后，如果点击另外一个弹出按钮，由于在body的click把这种情况排除了，所以那么这个弹出框并不会关闭。我们设置了$('.pop').popover('hide');就可以把所有的都关闭。带来的问题是，我们点击按钮来关闭自己的功能失效了，不过这个无伤大雅，毕竟点击任意其他内容都可以关闭。

相关代码最终应用于[LibRadar](http://radar.pkuos.org/)的结果展示页面。
