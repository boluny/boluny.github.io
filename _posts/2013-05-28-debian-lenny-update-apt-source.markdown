---
layout: post
title: "Debian Lenny更新apt源的问题"
date: 2013-05-28 15:37 +0800
type: tech
---
因为机器配置的问题，希望找一个内存占用比较小的，但是软件包又不能太旧，于是盯上了Debian 5.0（代号Lenny）。下好了安装包，一路next，直接进入gnome界面，目测应该是2.x版本，还没有到那么不喜欢的风格，跟 ubuntu8.04类似的界面，至于其他方面的配置都差不多。界面就不截了。

我没有下载DISK2，因此有些包没有安装，比如gcc之类的，所以希望通过APT来进行软件包的管理。可是用自带的`source.list`中的源，已经找不到这些软件包了，应该是不再支持，所以在主服务器上不再提供，后来不死心，去Debian官网上找啊找，找到了一个[http://archive.debian.org/](http://archive.debian.org/)的位置，其中有将旧版本的一些软件包都archive了，看看说明，里面还有对Lenny的软件包的支持，在pool文件夹下也可以看到相应的一些包，那么接下来就是简单多了，执行以下语句：
 
    sudo vim /etc/apt/source.list

然后将如下两行加入到文件中：

   
    # In the archived path on debian.org
    deb http://archive.debian.org/debian contrib main non-free

    # For source files
    deb-src http://archive.debian.org/debian contrib main non-free

至于原来文件中的一些源，直接注释掉或者删掉即可。在行首加上#表示该行是注释，同shell

但是这时候也出现了问题，APT还是无法获得软件包，后来在gnome界面上的software管理器中，点击更新了一些地址，然后重新载入，终于可以下载了，虽然上面的软件包不够新，但是依然可以做学习练习使用，无障碍。

经过查阅和认真的分析，我用gnome后来执行的操作应当是如下命令：

    sudo apt-get update

即把`source.list`中的deb地址的一些元数据信息（如软件包名称和版本）下载到本地了，然后再执行`apt-get install`等方法即无碍。

又另另，[http://mirrors.163.com](http://mirrors.163.com) 上也有Lenny的archive包，可以作为源使用，国内的话用起来应该比Debian官网的速度要快。

