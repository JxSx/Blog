---
title: 根Activity组件的启动过程
date: 2017-04-13 16:42:00
tags:
- Activity启动过程
categories: Android  
---
图例只描述了Activity组件在进程外的启动过程，即从Launcher点击图标启动MainActivity的过程。

MainActivity的启动过程涉及到了三个进程。MainActivity组件、LauncherActivity组件和ActivityManagerService组件分别运行在不同的进程中。

![Activity启动过程](93730-6eec539f556d1287.webp)

<!-- more -->

在第23步中会首先创建进程，流程如下图
![](93730-a705c168add681b2.webp)

其中ActivityManagerService和Process运行在system_server进程中，启动新的进程时，需要system_server与zygote进程进行socket通信，从而fork出新的进程。执行RuntimeInit.zygoteInit() -> RuntimeInit.applicationInit() -> RuntimeInit.invokeStaticMain()
```
    public static final void zygoteInit(int targetSdkVersion, String[] argv, ClassLoader classLoader)
            throws ZygoteInit.MethodAndArgsCaller {
        if (DEBUG) Slog.d(TAG, "RuntimeInit: Starting application from zygote");

        Trace.traceBegin(Trace.TRACE_TAG_ACTIVITY_MANAGER, "RuntimeInit");
        redirectLogStreams();

        commonInit();
        nativeZygoteInit();
        applicationInit(targetSdkVersion, argv, classLoader);
    }
```

```
**RuntimeInit.invokeStaticMain()**
//className的值是“android.app.ActivityThread”
private static void invokeStaticMain(String className, String[] argv, ClassLoader classLoader)
            throws ZygoteInit.MethodAndArgsCaller {
        Class<?> cl;

        try {
            cl = Class.forName(className, true, classLoader);
        } catch (ClassNotFoundException ex) {
            ......
        }

        Method m;
        try {
            m = cl.getMethod("main", new Class[] { String[].class });
        } catch (NoSuchMethodException ex) {
            ......
        } catch (SecurityException ex) {
            ......
        }
        ......
        /*
         * This throw gets caught in ZygoteInit.main(), which responds
         * by invoking the exception's run() method. This arrangement
         * clears up all the stack frames that were required in setting
         * up the process.
         */
        throw new ZygoteInit.MethodAndArgsCaller(m, argv);
    }
```

**注意**
ActivityThread.main方法的执行是通过抛异常的方式执行的。抛出MethodAndrArgsCaller异常，一直按照原路向上抛。在ZygoteInit.main方法中捕获异常，并调用run方法反射执行ActivityThread.main方法
```
public class ZygoteInit {
    public static void main(String argv[]) {
        try {
			......

            registerZygoteSocket(socketName);
			......
            preload();
			......
            if (startSystemServer) {
                startSystemServer(abiList, socketName);
            }

            runSelectLoop(abiList);

            closeServerSocket();
        } catch (MethodAndArgsCaller caller) {
			// 处理异常
            caller.run();
        } catch (RuntimeException ex) {
            Log.e(TAG, "Zygote died with exception", ex);
            closeServerSocket();
            throw ex;
        }
    }
}
```
