---
layout: post
title: "关于squid-3.3.1在gcc-4.3.2+glibc-2.6.1的连接问题"
date: 2013-06-04 15:31 +0800
type: tech
---
squid-3.3.1在crosstool下的环境下可以通过编译，但是连接的时候会出问题，从提示上看是cpu_affinity的问题，关于这一点，有许多文章讨论过，不再赘述，只是讲错误，连接错误提示’undefined reference to __sched_cpucount()’，这个问题开始考虑可以讲gcc连接的库置换为2.7，但是限于crosstool，在`LDFLAGS`添加的参数不起作用，这也是百思不得其解的地方。后来无奈，查阅代码，看到在squid-3.3.1代码中`./compat/cpu.h`里包换了对glibc- 2.7和2.6的支持，因为cpu_affinity始于libc2.6开始支持。可是好像是打包的libc版本问题，虽用`objdump`能看到该函数，但引用会失败。

所以天才的John&Sam组合想到，既然`./compat/cpu.`h文件中定义了`CPU_COUNT`和`CPU_AND`，那即使有已经定义的`CPU_COUNT`，屏蔽之，用squid自带的实现使用就好了，不得不说这是个天才的想法，于是在`./compat/cpu.h`中添加一段代码：
 
    #undef CPU_COUNT

在于
   
    #ifndef CPU_COUNT

之前，将之写入patch文件是一个更好的方案，这就不用改动tar包，可以在crosstool环境下完美连接成功。

这个问题困扰了好久了，暂时有个方案能把它过了也算对努力的肯定吧。

记录下以后也许会有用。

另外发现GCC还有许多都不太懂，-L,-l,-I等参数的使用，Makefile中如何设定`LDFLAGS`, `CPPFLAGS`, `CFLAGS`, `LIBS`等变量等也都是需要学习的，不过好在这方便的资料比较多，学起来会比较轻松一点。

