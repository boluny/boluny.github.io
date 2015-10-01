---
layout: post
title: "记录刚刚遇到的问题"
date: 2013-03-27 15:49 +0800
type: tech
---
为了一个小问题浪费了快1个小时的时间，以后还是多注意细节

废话少说，这次的问题是关于python画图的，google了一番下载了matplotlib画图，敲入sample结果提示找不到库，想自己是 不是漏装了什么东西，然后上官网一看，果然是requirement里包括了一个numpy的依赖库，还有推荐一个ipython的工具，不过标记是 optional，那就先不管吧，于是去下载numpy，不小心下载了源代码，懒得编译，看看有没有现成的，诶，找到一个，装上去。准备在idle里试试 有没有，结果，果断是找不到模块名。目测了一下，嗯，机器是64位的，自己下载的是32位的安装程序，当然没法用。只好卸载了，继续下载了两个64位的 包，其中特别注意numpy官方没有64位版的，但有非官方的编译版本提供，地址如下：

[http://www.lfd.uci.edu/~gohlke/pythonlibs/#numpy](http://www.lfd.uci.edu/~gohlke/pythonlibs/#numpy)

注意有MKL优化和非优化版本的区别。结果装上去这两个64位之后，在cmd里测试一下import居然还没有，震惊了。果断是继续排查，后来在翻 我的电脑目录的时候，发现C盘和D盘都有python文件夹，难道是这个原因？然后一看，果然是，cmd里的版本是2.7.2，32位版，而IDLE是 2.7.3的64位版。怪不得总是出这样或那样的问题，把32位版本的卸掉，64位的在环境变量设置一下路径，OK，出结果了，跑一个demo效果如下：

[此处有图，然而已残殁]

另外顺便一说，python extension的包一般通过控制面板里删，点python目录下的remove***.exe删会报runtime error “program is started by Windows”。

————————————————————————————————————

另外关于用matplotlib绘图时中文字体显示的问题参照下文：

[http://blog.csdn.net/KongDong/article/details/4338826](http://blog.csdn.net/KongDong/article/details/4338826)

