---
title: ReactNative入门
date: 2016-10-14 16:42:00
tags: 
- ReactNative环境搭建
categories: ReactNative

---
目前主流应用程序大体分为三类：`Native App`, `Web App`, `Hybrid App`.
![示例（来源网络）.png](93730-adb52d444d71a1ad.webp)

三者各有利弊，具体对比差异[点击这里](http://www.uisdc.com/web-hybrid-native-app)

<b>`React Native`</b> 是 `Facebook` 推出的一个用 `Java` 语言就能同时编写 `ios`，`android`，以及后台的一项技术。 `FaceBook` 也号称这们技术是 “`Learn Once，Write AnyWhere`”。
<!-- more -->
<b>`React Native`的优势：</b>
* 相对`Webapp`：
>不用`Webview`，彻底摆脱了`Webview`让人不爽的交互和性能问题
有较强的扩展性，这是因为`Native`端提供的是基本控件，`JS`可以自由组合使用

* 相对于`Native app`：
>可以通过更新远端`JS`，实现热更新。

是不是已经迫不及待运行看看了......
<b>安装</b>
- <b>Chocolatey</b>
[`Chocolatey`](https://chocolatey.org/)是一个`Windows`上的包管理器，类似于`linux`上的`yum`和 `apt-get`。
- <b>Python 2</b>
打开命令提示符窗口，使用`Chocolatey`来安装`Python 2`.
>choco install python2

- <b>Node</b>
打开命令提示符窗口，使用`Chocolatey`来安装`NodeJS`.
>choco install nodejs.install

- <b>React Native命令行工具（react-native-cli）</b>
`React Native`的命令行工具用于执行创建、初始化、更新项目、运行打包服务`packager`等任务。
>npm install -g react-native-cli

当然作为一名有素质的`Android Coder`，`JDK` 、`Android Studio` 、`Git` 、`Emulator`不能少。

<b>测试安装</b>
启动模拟器，cmd下键入
```
react-native init AwesomeProject
cd AwesomeProject
react-native run-android
```

项目目录结构：
![项目目录.png](93730-6587e76d08aa81df.webp)
运行命令
![image1.png](93730-8c9704ee0132de3c.webp)
![image2.png](93730-928b549b7583cb62.webp)
模拟器运行结果：
![模拟器运行结果（背景色有修改）.png](93730-c52244dd079b6687.pngwebp)


有个常见的问题是在你运行`react-native run-android `命令后，`Packger`可能不会自动运行。此时你可以手动启动它
```
cd AwesomeProject
react-native start
```
<b>修改项目</b>
现在你已经成功运行了项目，我们可以开始尝试动手改一改了：
使用你喜欢的文本编辑器打开`index.android.js`
并随便改上几行，按两下`R`键，或是用`Menu`键（通常是`F2`，在`Genymotion`模拟器中是`⌘+M`）打开开发者菜单，然后选择 *`Reload JS`* 就可以看到你的最新修改。

完成！

参考链接：
[ReactNative中文网](http://reactnative.cn/docs/0.31/getting-started.html)
[ReactNative官网](http://facebook.github.io/react-native/docs/getting-started.html)
[ReactNative基础教程](http://wiki.jikexueyuan.com/list/react/)