---
layout: post
title: "Linux知识归纳"
date: 2013-05-16 15:42 +0800
type: tech
---
因为工作所需，所以得在Linux环境下进行一些学习和配置，所以没有办法，最好的方式就是把他们记录下来，忘记了也可以来差。

使用的环境是Ubuntu 12.04 LTS

###基本知识：

使用`Ctrl + Alt + T`可以是终端打开的快捷方式，这12.04一升级，要找到终端不容易啊。

`home/[user]/.bashrc`可以修改环境变量，其实就是在terminal执行前执行其中的一些设置

`home/[user]/.vimrc`可以配置vim，说到vim，就想到了预装的vim-common，必须先卸载掉。 执行`$sudo apt-get remove vim-common`

然后执行`$sudo apt-get install vim`即可。否则没有各种tab自动完成功能，很心碎的。

另外管道功能真的很强大 比如`env | grep P4` 这样的用法，直接省却了多少麻烦啊。

另外好像通配符功能很强大，比如`cp ./he* ./test/` 将he开头的文件全部拷到别处，省了很多麻烦啊，比如要拷库文件就是 `cp lib*`，当然这个功能不知道是不是跟正则表达式有关，如果是的话，只能说太强大了。

###常用命令归纳：

cp: 拷贝文件的方法，唯有一点需要注意，在有子文件夹时可以考虑添加-r参数

rm: 删除文件/文件夹的方法，注意当要删除的文件夹有子文件夹时 添加-r参数

chown: 改变文件/文件夹的owner，一般用法是`chown [user] [file]`

chmod: 改变文件/文件夹的读写执行权限 `chmod [right] [file]`

mv: 剪切或者重命名文件 在同一个路径下就是重命名了

cat: 很有用，我一般是用来把文件定向到standard output，也可以定位到文件，或者合并两个文件成一个新文件，很有用。

env: 用来查看系统的环境变量。

export: 可以临时在terminal中引入环境变量

find: 用来查找文件通过文件名

grep: 查找文件中的内容，用法可以baidu。

du -sm [file] 以MB为单位返回文件/文件夹大小   -sh 以GB为单位返回大小

###小技巧tips：

`/proc/cpuinfo`可以显示cpu信息

`/proc/meminfo`可以显示内存的信息

替换`/etc/apt/source.list`可以获得更好的源，我用的是163的镜像，地址是[http://mirrors.163.com](http://mirrors.163.com)，上面其实也有很多ISO可以下载。

速度应该比[en.archive.ubuntu.com](en.archive.ubuntu.com)要快那么一点点吧

`ldd -v`可以了解到glibc的版本，同样可以到/lib/libc.6.so执行`./libc.6.so`看glibc的版本。

