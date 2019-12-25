---
title: Android系统全貌
date: 2017-09-12 16:13:31
tags:
- Android系统全貌
categories: Android
---
>转自Gityuan的[Android开篇](http://gityuan.com/android/#syscall--jni)，对自我学习作进一步整理。

>Android系统以`Linux内核 `作为基底，上层采用`Native层`和`Java层`。系统分为内核空间和用户空间，并通过系统调用（`Syscall`）连通。用户空间主要采用C++和Java代码实现，通过`JNI`技术连通Java层和Native层（C/C++）。

![图片来源  [GITYUAN](http://gityuan.com/android/)](1.jpg)
<!-- more -->
# Loader层
* <b>Boot ROM</b>: 电源按下,引导芯片代码开始从预定义的地方（固化在ROM）开始执行.加载引导程序到`RAM`,然后执行.
* <b>Boot Loader</b>: 这是`Android`系统系统之前的引导程序，主要用来检测外部的`RAM`以及设置网络、内存、初始化硬件参数等.

#Kernel层
Kernel层是指`Android`内核层，到这里才刚刚开始进入`Android`系统。
* 启动`Kernel`的<b>swapper进程</b>(pid=0)：该进程又称为`idle`进程, 系统初始化过程`Kernel`由无到有开创的第一个进程, 用于初始化进程管理、内存管理，加载`Display,Camera Driver，Binder Driver`等相关工作(图中`kernel`层蓝色区块)；
* 启动<b>kthreadd进程</b>（pid=2）：是`Linux`系统的内核进程，会创建内核工作线程`kworkder`，软中断线程`ksoftirqd，thermal`等一系列内核守护进程。`kthreadd进程是所有内核进程的父进程。`

`Linux`内核加载主要包括初始化`kernel`核心（内存初始化，打开中断，初始化进程表等）、初始化驱动、启动内核后台（`daemons`)线程、安装根（`root`)文件系统等。后续启动第一个用户级进程`init`（pid=1）.

#Native层
`Native层`主要包括启动`init进程`（`Android`的第一个用户空间进程）、`HAL层`（[硬件抽象层](http://baike.baidu.com/link?url=eyv4w4VuCQxHW3GGJvJ4QDDbBvWbjPZYjo5GLaFRZE0xkm5Cu58zqOr6ZaexIEWsi7bCWUoKehTUN5WsHumhR2HGB4KXh8ruMbnXvDdlVZH5_hGk8Zi9nB5Op_qY8Ixn)）以及`开机动画`等。`init进程是所有用户进程的鼻祖`。同时`init进程`也会孵化一系列用户进程，还会启动关键的服务以及孵化`Zygote进程`。
* `init进程`会孵化出`ueventd、logd、healthd、installd、adbd、lmkd`等用户守护进程；
* `init进程`还启动`servicemanager(binder服务管家)、bootanim(开机动画)`等重要服务
* `init进程`孵化出`Zygote进程`，`Zygote进程`是Android系统的第一个`Java进程`，`Zygote是所有Java进程的父进程`，`Zygote进程`本身是由`init进程`孵化而来的。

> 
* kthreadd进程: 是所有内核进程的父进程
* init进程 ： 是所有用户进程的父进程(或者父父进程)
* zygote进程 ： 是所有上层Java进程的父进程，另外zygote的父进程是init进程。

#Framework层
如图，`Framework层`分为`Java Frameword`和`C++ Framework`，分别由`system_server进程`和`media_server进程`负责启动和管理。

`Zygote`本身是一个`Native`的应用程序，刚开始的名字为`“app_process”`，运行过程中，通过系统调用将自己名字改为`Zygote`。在图中的红色线，便是`Zygote fork`出来的进程，所有的`App进程`都是由`Zygote fork`产生的。
![image.png](2.webp)

* `Zygote进程`，是由init进程通过解析init.rc文件后fork生成的，Zygote进程主要包含：
加载ZygoteInit类，注册Zygote Socket服务端套接字；
加载虚拟机；
preloadClasses；
preloadResouces。
* `System Server进程`，是由Zygote进程fork而来，System Server是Zygote孵化的第一个进程，System Server负责启动和管理整个Java framework，包含ActivityManager，PowerManager等服务。
* `Media Server进程`，是由init进程fork而来，负责启动和管理整个C++ framework，包含AudioFlinger，Camera Service，等服务。



#App层
* `Zygote进程孵化出的第一个App进程是Launcher`，这是用户看到的桌面App；
* Zygote进程还会创建Browser，Phone，Email等App进程，每个App至少运行在一个进程上。
* 所有的App进程都是由Zygote进程fork生成的。

# Syscall && JNI
* Native与Kernel之间有一层系统调用(SysCall)层，见[Linux系统调用(Syscall)原理](http://gityuan.com/2016/05/21/syscall/);
* Java层与Native(C/C++)层之间的纽带JNI，见[Android JNI原理分析](http://gityuan.com/2016/05/28/android-jni/)。


[Andorid的启动过程](http://www.tuicool.com/articles/U3UvIrY)
[[Android进程整理](http://www.cnblogs.com/android-blogs/p/5632549.html)](http://www.cnblogs.com/android-blogs/p/5632549.html)