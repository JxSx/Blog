---
title: Android-Architecture之todo-mvp
tags:
  - Architecture
  - MVP
categories: Android
abbrlink: 29f4a5f2
date: 2017-02-28 17:05:16
---
> [todo-mvp官方地址](https://github.com/googlesamples/android-architecture/tree/todo-mvp)

#UI层
项目主要实现了4部分功能：
- Tasks 用于管理任务列表
- TaskDetails 用于显示和删除任务
- AddEditTask 用于创建和编辑任务
- Statistics 显示与任务相关的统计信息

# 具体实现
每个功能都是依照如下方式实现：
- 定义一个连接View和Presenter的契约类Contract
- 创建一个Presenter，并实现契约类中的Presenter接口
- Activity中实例化Fragment和Presenter。
- Fragment中实现BaseView接口
<!--more-->
>A presenter typically hosts business logic associated with a particular feature, and the corresponding view handles the Android UI work. The view contains almost no logic; it converts the presenter's commands to UI actions, and listens for user actions, which are then passed to the presenter.

Presenter通常托管这与特定功能相关的业务逻辑，响应的View层处理Android UI工作。视图层几乎不包含逻辑；视图层把Presenter层中的命令转化为UI操作，而且视图层去监听用户的操作，传递给Presenter层。

#架构图
![mvp.png](93730-8e8540065d3aafd6.webp)


项目中包含了Presenter，Repository和DataSource，实现层与层之间的解耦，同时方便单元测试。

按照[Android-CleanArchitecture](https://github.com/android10/Android-CleanArchitecture)架构图来说，这里只包含了Presentation Layer和Data Layer两层，并未包含Domain Layer。其依赖规则是只能上层依赖下层。

#Data层
App需要的数据都是通过Data层中的Repository模块提供，由它去完成访问网络和数据库等。
>使用Repository将业务层与具体的查询逻辑分离，Repository的实现类主要职责就是存储查询数据，一个更简单的解释就是你可以把Repository的实现类当成是一个Java容器(Collections)，可以从中获取或存储数据(add/remove/etc),他对数据源进行了抽象。Data层的Repository只需要实现相关接口，并提供相关服务即可。

![Respository.png](93730-8ec0e1015a3667b5.webp)

如上图，Repository接口定义了getUserById、getAllUsers等方法，其实现类对接口做具体实现，而真正的操作可能是通过Dao才能访问数据库，或者通过Okhttp访问网络url获取数据。

回到todo项目中：
- 接口TasksDataSource  规范操作数据的方法
- 类TasksRemoteDataSource 实现TasksDataSource 接口，实现真正操作数据的逻辑。
- 类TasksRepository 实现TasksDataSource接口，持有TasksRemoteDataSource对象调用各个方法。
![Class Diagram.png](93730-a73c8794fb7722ac.webp)

欢迎交流！
[Clean架构探讨](http://www.jianshu.com/p/66e749e19f0d)
