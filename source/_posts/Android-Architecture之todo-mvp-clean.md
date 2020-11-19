---
title: Android-Architecture之todo-mvp-clean
tags:
  - Architecture
  - MVP
categories: Android
abbrlink: e367b974
date: 2017-03-13 17:05:16
---

> Domain Layer拥有所有的业务逻辑。Domain层从命名为User Case或者interactor的类开始，这些类由展示层（Presenter）来使用。这些User Case代表开发人员可以从展示层（Presenter）执行的所有可能的操作。

[上一篇文章](http://www.jianshu.com/p/0368481cc419)描述了todo-mvp，并着重描述了一下Data层。这次在此基础上又增加了domain 层。

![Paste_Image.png](93730-77aefec441b06e69.webp)
<!--more-->
# 主要概念
与之前项目最大的区别是扩展出了Domain层和许多use case。这将有助于避免Presenter上的代码重复。

User Case定义应用程序需要的操作（封装了请求参数和回调结果基类）。将Data层的方法抽象到了Domain层的类，这增加了可读性，因为类的名称可以使目的更明显，见名知义。

User Case更方便我们对Domain层的代码进行重用操作。CompleteTask集成了User Case,TaskDetailPresenter和TasksPresenter都在使用它，得到了重用。

这些User Case的执行在后台线程中使用[命令模式](http://www.oodesign.com/command-pattern.html)来完成。Domain层与Android SDK或其他第三方库完全解耦。

**命令模式优点**：
1. 命令模式是通过命令发送者和命令执行者的解耦来完成对命令的具体控制的。
2. 命令模式是对功能方法的抽象，并不是对对象的抽象。
3. 命令模式是将功能提升到对象来操作，以便对多个功能进行一系列的处理以及封装。

这里的命令发送者是Presenter，命令执行者是repository。中间通过usercase和其实现类完成两者解耦。

直观的看出，将getTask方法抽象成GetTask类。

# 杂谈
Domain层是对Data层和Presenter层之间的数据处理和操作加工。对于复杂的项目而言，上层UI和底层数据的变动频繁，导致UI和数据经常对不上号。因而中间层可以对数据进行一层过滤操作。拿到网络数据，加工成Presenter层需要的数据即可。

同时，将Presenter的方法含义抽象到类中，直观的显示功能操作，并封装了RequestValues和ResponseValue。

这一层，既不依赖与Android SDK，也不依赖于第三方库（网络库，数据库）。