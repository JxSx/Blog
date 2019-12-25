---
title: App集成ReactNative
date: 2016-10-21 16:17:59
tags:
- ReactNative
categories: ReactNative
---
1. 打开`cmd`，进入工程目录

  ```
  npm init   //提醒生成package.json文件
  ```
 这个命令提示需要输入部分信息，如图

  ![命令行](93730-d52e7236ee1fef23.webp)

<!-- more -->
 生成文件如下：
  ![package.json](93730-a015cb13a8bd359f.webp)
   当然，文件内容我们后续还可以使用编辑器修改。

* 安装`React`和`React Native`
```
npm install --save react react-native  //安装React 和React Native
```
* 下载`.flowconfig`文件
```
curl -o .flowconfig https://raw.githubusercontent.com/facebook/react-native/master/.flowconfig //下载.flowconfig文件
```
> 也可以手动创建.flowconfig文件，[点击这里]([https://raw.githubusercontent.com/facebook/react-native/master/.flowconfig](https://raw.githubusercontent.com/facebook/react-native/master/.flowconfig%E7%BD%91%E5%9D%80%E5%A4%8D%E5%88%B6%E5%86%85%E5%AE%B9%E5%88%9B%E5%BB%BA%E6%96%87%E4%BB%B6))复制文本内容到文件中
* 在`package.json`文件里`scripts`标签下添加`start`配置
```
"scripts": {
    "start": "node node_modules/react-native/local-cli/cli.js start"
  },
```
* 添加`index.android.js`到项目中
``` javascript
'use strict';
import React from 'react';
import {
  AppRegistry,
  StyleSheet,
  Text,
  View
} from 'react-native';
class HelloWorld extends React.Component {
  render() {
    return (
      <View style={styles.container}>
        <Text style={styles.hello}>Hello, World</Text>
      </View>
    )
  }
}
var styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
  },
  hello: {
    fontSize: 20,
    textAlign: 'center',
    margin: 10,
  },
});
AppRegistry.registerComponent('HelloWorld', () => HelloWorld);
```
* 在`app`模块下 `build.gradle`配置
```
dependencies {
...
compile "com.facebook.react:react-native:+" // From node_modules.
}
```
 
  >  **注意：** 最新版本中支持的是23，appcompat-v7:23.0.1**，暂时没有试24的api

* 整个工程下`build.gradle`配置
```
allprojects {
repositories {
    ...
    maven {
        // All of React Native (JS, Android binaries) is installed from npm
        url "$rootDir/node_modules/react-native/android"
    }
}
...
}
```
* 添加权限
在`AndroidManifest.xml`添加`<uses-permission android:name="android.permission.INTERNET" />`

* 集成ReactActivity
```
public class MyReactActivity extends ReactActivity {
    @Nullable
    @Override
    protected String getMainComponentName() {
        return "HelloWorld";//组件名
    }
}
```
* 在`Terminal`中启动服务
```
npm start 
//等效`package.json`中的`node node_modules/react-native/local-cli/cli.js start`
```
    运行 `npm start`，看到下图表示启动成功
  ![image.png](93730-86580a6c063b3c4e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)
* 运行模拟器，启动定义的MyReactActivity即可


---

参考链接：
[史上最详细的Android原生APP中添加ReactNative 进行混合开发教程](http://www.jianshu.com/p/22aa14664cf9?open_source=weibo_search)
[原生 Android 项目集成 React Native](http://www.jianshu.com/p/fc29c86fc2b8)