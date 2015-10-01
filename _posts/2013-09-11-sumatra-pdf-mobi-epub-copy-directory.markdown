---
layout: post
title: "Sumatra PDF看mobi、epub时无法复制及没目录的解决方法"
date: 2013-09-11 15:52 +0800
type: tech
---
很早之前就用上了SumatraPDF,这个软件还是很不错的，基于MuPDF的windows平台专用 的阅读器，支持多种格式如PDF, eBook (ePub, Mobi), XPS, DjVu, CHM, Comic Book (CBZ and CBR)等，官网是：[Sumatra PDF官网](http://www.sumatrapdfreader.org/free-pdf-reader.html)。 之所以用它是因为PDF渲染速度快，比Adobe亲儿子Adobe Reader要快，虽然功能也少很多，比如笔记注释等，但是基本的阅读功能是有的。而现在下了一部分mobi和epub格式的电子书，有的时候想看这些书 的目录，但是默认的界面没有办法能看到标签或者目录，这样一来不方便跳转，从而降低了这个工具的易用性。
 后来搜了一下，其实是有解决办法的，就是使用看PDF时的UI，而不是看这些电子书的UI。然后就有目录了，另外也可以选择然后复制黏贴了。具体的办法见点这里看解决方法。也就是修改advanced options的配置文件（注，2.3以上的版本才有advanced option的配置），在其中有这样一段：

    EbookUI [
    name of the font. takes effect after re-opening the document
    FontName = Georgia 
    
    size of the font. takes effect after re-opening the document
    FontSize = 12.5
    
    color for text
    TextColor = #5f4b32
    
    color of the background (page)
    BackgroundColor = #fbf0d9
    
    if true, the UI used for PDF documents will be used for ebooks as well (enables printing and searching, disables automatic reflow)
    UseFixedPageUI = false
    ]

注意其中的UseFixedPageUI,需要修改的就是这个，修改为`UseFixedPageUI = true`，保存配置文件，退出重新打开，看到的界面就是PDF界面，带书签的，也就基本满足需求了。

