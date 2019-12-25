---
title: 使用Gradle查看Android项目中库的依赖关系
date: 2017-08-07 15:52:00
tags: 
- Gradle
- dependencies 
categories: Android
---
在`Terminal`中，可以通过 `gradle` 的命令查看项目中所使用库的版本，并且可以更加直观**看到库之间的依赖关系**。同时它们可以帮助您跟踪并**解决与库版本冲突**有关的任何问题。[Building Android apps](https://guides.gradle.org/building-android-apps/)


----------

# dependencies
**指令**：
> $ ./gradlew :app:dependencies --configuration compile

**说明**：
在`Window`系统下，无需使用`./`开头，直接使用gradlew即可。
执行`app`模块下的`dependencies`任务；额外配置`compile`,编译环境下的依赖项。

<!-- more -->

**结果**：
```
:app:dependencies

------------------------------------------------------------
Project :app
------------------------------------------------------------

compile - Classpath for compiling the main sources.
+--- com.android.support:appcompat-v7:25.3.1
|    +--- com.android.support:support-annotations:25.3.1
|    +--- com.android.support:support-v4:25.3.1
|    |    +--- com.android.support:support-compat:25.3.1
|    |    |    \--- com.android.support:support-annotations:25.3.1
|    |    +--- com.android.support:support-media-compat:25.3.1
|    |    |    +--- com.android.support:support-annotations:25.3.1
|    |    |    \--- com.android.support:support-compat:25.3.1 (*)
|    |    +--- com.android.support:support-core-utils:25.3.1
|    |    |    +--- com.android.support:support-annotations:25.3.1
|    |    |    \--- com.android.support:support-compat:25.3.1 (*)
|    |    +--- com.android.support:support-core-ui:25.3.1
|    |    |    +--- com.android.support:support-annotations:25.3.1
|    |    |    \--- com.android.support:support-compat:25.3.1 (*)
|    |    \--- com.android.support:support-fragment:25.3.1
|    |         +--- com.android.support:support-compat:25.3.1 (*)
|    |         +--- com.android.support:support-media-compat:25.3.1 (*)
|    |         +--- com.android.support:support-core-ui:25.3.1 (*)
|    |         \--- com.android.support:support-core-utils:25.3.1 (*)
|    +--- com.android.support:support-vector-drawable:25.3.1
|    |    +--- com.android.support:support-annotations:25.3.1
|    |    \--- com.android.support:support-compat:25.3.1 (*)
|    \--- com.android.support:animated-vector-drawable:25.3.1
|         \--- com.android.support:support-vector-drawable:25.3.1 (*)
+--- com.android.support:support-v4:25.3.1 (*)
+--- com.android.support:recyclerview-v7:25.3.1
|    +--- com.android.support:support-annotations:25.3.1
|    +--- com.android.support:support-compat:25.3.1 (*)
|    \--- com.android.support:support-core-ui:25.3.1 (*)
+--- com.android.support:design:25.3.1
|    +--- com.android.support:support-v4:25.3.1 (*)
|    +--- com.android.support:appcompat-v7:25.3.1 (*)
|    +--- com.android.support:recyclerview-v7:25.3.1 (*)
|    \--- com.android.support:transition:25.3.1
|         +--- com.android.support:support-annotations:25.3.1
|         \--- com.android.support:support-v4:25.3.1 (*)
+--- com.yqritc:recyclerview-flexibledivider:1.2.9
+--- com.belerweb:pinyin4j:2.5.0
+--- com.j256.ormlite:ormlite-android:5.0
|    \--- com.j256.ormlite:ormlite-core:5.0
+--- com.j256.ormlite:ormlite-core:5.0
+--- com.alibaba:arouter-api:1.2.1.1
|    +--- com.alibaba:arouter-annotation:1.0.3
|    \--- com.android.support:support-v4:25.2.0 -> 25.3.1 (*)
+--- pub.devrel:easypermissions:0.4.0
+--- com.github.CymChad:BaseRecyclerViewAdapterHelper:2.9.18
+--- com.afollestad.material-dialogs:core:0.9.4.5
|    +--- com.android.support:support-v13:25.3.1
|    |    +--- com.android.support:support-annotations:25.3.1
|    |    \--- com.android.support:support-v4:25.3.1 (*)
|    +--- com.android.support:appcompat-v7:25.3.1 (*)
|    +--- com.android.support:recyclerview-v7:25.3.1 (*)
|    +--- com.android.support:support-annotations:25.3.1
|    \--- me.zhanghai.android.materialprogressbar:library:1.4.1
|         +--- com.android.support:appcompat-v7:25.3.1 (*)
|         \--- com.android.support:support-annotations:25.3.1
+--- com.alibaba:fastjson:1.2.32
\--- com.esri.arcgis.android:arcgis-android:10.2.9

(*) - dependencies omitted (listed previously)

BUILD SUCCESSFUL

Total time: 11.69 secs

```
从中可以发现44行：`com.alibaba:arouter-api:1.2.1.1`依赖的`support-v4`库版本比当前环境版本低，因此可以使用`exclude`剔除旧版本库，避免冲突！
```
compile('com.alibaba:arouter-api:1.2.1.1') {
        exclude module: 'support-v4'
}
```


----------

# dependencyInsight
**指令**：
> $ ./gradlew :app:dependencyInsight --dependency fastjson --configuration compile

**说明**：
查看指定库的依赖关系

**结果**：
```
:app:dependencyInsight
com.android.support:support-annotations:25.3.1
+--- com.afollestad.material-dialogs:core:0.9.4.5
|    \--- compile
+--- com.android.support:appcompat-v7:25.3.1
|    +--- compile
|    +--- com.android.support:design:25.3.1
|    |    \--- compile
|    +--- com.afollestad.material-dialogs:core:0.9.4.5 (*)
|    \--- me.zhanghai.android.materialprogressbar:library:1.4.1
|         \--- com.afollestad.material-dialogs:core:0.9.4.5 (*)
+--- com.android.support:recyclerview-v7:25.3.1
|    +--- compile
|    +--- com.android.support:design:25.3.1 (*)
|    \--- com.afollestad.material-dialogs:core:0.9.4.5 (*)
+--- com.android.support:support-compat:25.3.1
|    +--- com.android.support:support-v4:25.3.1
|    |    +--- compile
|    |    +--- com.android.support:appcompat-v7:25.3.1 (*)
|    |    +--- com.android.support:design:25.3.1 (*)
|    |    +--- com.alibaba:arouter-api:1.2.1.1
|    |    |    \--- compile
|    |    +--- com.android.support:transition:25.3.1
|    |    |    \--- com.android.support:design:25.3.1 (*)
|    |    \--- com.android.support:support-v13:25.3.1
|    |         \--- com.afollestad.material-dialogs:core:0.9.4.5 (*)
|    +--- com.android.support:recyclerview-v7:25.3.1 (*)
|    +--- com.android.support:support-vector-drawable:25.3.1
|    |    +--- com.android.support:appcompat-v7:25.3.1 (*)
|    |    \--- com.android.support:animated-vector-drawable:25.3.1
|    |         \--- com.android.support:appcompat-v7:25.3.1 (*)
|    +--- com.android.support:support-media-compat:25.3.1
|    |    +--- com.android.support:support-v4:25.3.1 (*)
|    |    \--- com.android.support:support-fragment:25.3.1
|    |         \--- com.android.support:support-v4:25.3.1 (*)
|    +--- com.android.support:support-core-utils:25.3.1
|    |    +--- com.android.support:support-v4:25.3.1 (*)
|    |    \--- com.android.support:support-fragment:25.3.1 (*)
|    +--- com.android.support:support-core-ui:25.3.1
|    |    +--- com.android.support:support-v4:25.3.1 (*)
|    |    +--- com.android.support:recyclerview-v7:25.3.1 (*)
|    |    \--- com.android.support:support-fragment:25.3.1 (*)
|    \--- com.android.support:support-fragment:25.3.1 (*)
+--- com.android.support:support-core-ui:25.3.1 (*)
+--- com.android.support:support-core-utils:25.3.1 (*)
+--- com.android.support:support-media-compat:25.3.1 (*)
+--- com.android.support:support-v13:25.3.1 (*)
+--- com.android.support:support-vector-drawable:25.3.1 (*)
+--- com.android.support:transition:25.3.1 (*)
\--- me.zhanghai.android.materialprogressbar:library:1.4.1 (*)

(*) - dependencies omitted (listed previously)

BUILD SUCCESSFUL

Total time: 13.388 secs

```