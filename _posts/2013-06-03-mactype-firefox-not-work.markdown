---
layout: post
title: "解决MacType在FireFox中不起作用"
date: 2013-06-03 15:35 +0800
type: tech
---
下载了一个软件MacType，是一个字体美化的软件，对XP的效果极好，但WIN7本身字体显示已经比较不错了，所以MacType的使用效果不如XP那般明显，但也能看得出有所提升。

接下去的问题就是WIN7下FireFox并不会出现预期的效果，虽然字体变了，但是变细变淡了，反而效果不如之前，而在Chrome中确无如此问 题。后来找到了解决方案，需要修改一下设置，即在FireFox中选项->高级->常规，去掉对硬件加速的选择。然后重启FireFox即可 发现显示效果已跟Chrome中类似了。

