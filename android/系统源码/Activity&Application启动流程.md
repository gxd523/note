[TOC]

### 启动app过程
![](https://github.com/gxd523/note/raw/master/pic/launch_app.png)

* 点击桌面APP图标时，Launcher的startActivity()方法，通过Binder通信，调用`system_server`进程中AMS服务的`startActivity()`，发起启动请求
* `system_server`进程接收到请求后，通过Socket通信，向Zygote进程发送创建进程的请求
* Zygote进程fork出App进程，并执行`ActivityThread.main()`，创建ActivityThread线程，初始化MainLooper，主线程Handler，同时初始化ApplicationThread用于和AMS通信交互
* App进程，通过Binder向`sytem_server`进程发起attachApplication请求，这里实际上就是APP进程通过Binder调用`sytem_server`进程中AMS的`attachApplication()`，将ApplicationThread对象与AMS绑定
* `system_server`进程在收到attachApplication的请求，进行一些准备工作后，再通过binder IPC向App进程发送`ApplicationThread.bindApplication()`请求（初始化Application并调用onCreate方法）和`ApplicationThread.scheduleLaunchActivity()`请求（创建启动Activity）
* App进程的binder线程（ApplicationThread）在收到请求后，通过handler向主线程发送`BIND_APPLICATION`和`LAUNCH_ACTIVITY`消息，这里注意的是AMS和主线程并不直接通信，而是AMS和主线程的内部类ApplicationThread通过Binder通信，ApplicationThread再和主线程通过Handler消息交互。 
* 主线程在收到Message后，创建Application并调用onCreate方法，再通过反射机制创建目标Activity，并回调Activity.onCreate()等方法
* 到此，App便正式启动，开始进入Activity生命周期，执行完onCreate/onStart/onResume方法，UI渲染后显示APP主界面

![](https://github.com/gxd523/note/raw/master/pic/ActivityLaunchProcess.png)

### ActivityThread
> 代表UI线程/主线程，但没有继承或实现任何类或接口，它的main()方法是APP的真正入口

* `main()`：创建`ActivityThread`实例，并调用`attach()`，其次是创建并执行`loop()`
* `attach()`：调用`ActivityManagerService.attachApplication(applicationThread)`
* `handleLaunchActivity()`：调用`performLaunchActivity()`
* `performLaunchActivity()`：通过`Instrumentation.newActivity()`创建`Activity`实例，并调用`Activity.attach()`,最后还调用了`Instrumentation.callActivityOnCreate()`

* `handleBindApplication()`：调用了`ActivityThread.AppBindData`成员变量`LoadedApk.makeApplication()`，最终里面调用了`Instrumentation.newApplication()`及`Instrumentation.callApplicationOnCreate()`

#### ApplicationThread
> 一个实现了IBinder接口的ActivityThread内部类，用于ActivityThread和AMS的所在进程间通信

* `scheduleLaunchActivity()`：通过向mH发送`LAUNCH_ACTIVITY`消息，调用`ActivityThread.handleLaunchActivity()`
* `bindApplication()`：通过向`mH`发送`BIND_APPLICATION`消息，调用`ActivityThread.handleBindApplication()`

#### Instrumentation
* `ActivityThread`的成员变量，可以理解为`ActivityThread`的一个工具类，一个进程只存在一个Instrumentation对象
* 在每个Activity初始化时，会通过Activity的`attach()`将该引用传递给Activity。Activity所有生命周期的方法都由该类来执行
* `newActivity()`：`classLoader.loadClass(activityClassName).newInstance()`创建Activity实例
* `callActivityOnCreate()`：调用`Activity.performCreate()`
* `newApplication()`：`classLoader.loadClass(activityClassName).newInstance()`创建`Application`，并调用`Application.attach()`，里面再调用`Application.attachBaseContext()`
* `callApplicationOnCreate()`：里面就是调用`Application.onCreate()`

### ActivityManagerService
> 即AMS，是一个服务端对象，负责所有的Activity的生命周期，AMS通过Binder与Activity通信，而AMS与Zygote之间是通过Socket通信

* `IActivityManager`：aidl接口
* `ActivityManager`：内部方法均是由ActivityManagerNative.getDefault()，获得`IActivityManager`，调用`IActivityManager`相应的方法代理实现的
* `attachApplication()`：调用了`attachApplicationLocked()`
* `attachApplicationLocked()`：将传入的`ApplicationThread`实例绑定到`ActivityManagerService`，调用了`ApplicationThread.bindApplication()`，其次是调用`ActivityStackSupervisor.attachApplicationLocked()`去创建`Activity` 

#### ActivityStackSupervisor
* `ActivityManagerService`的成员变量
* `attachApplicationLocked()`：调用了`realStartActivityLocked()`
* `realStartActivityLocked()`：调用了`ActivityThread.ApplicationThread.scheduleLaunchActivity()`

### PhoneWindow
* 在`Activity.attach()`里创建实例
* `mContentParent`：id为content的FrameLayout
* `mContentRoot`：`DecorView`的子View

### Activity
* `attach()`：在ActivityThread.performLaunchActivity()中被调用，初始化mWindow

### PolicyManager
* `makeNewWindow()`：调用`Policy.makeNewWindow()`
* `makeNewLayoutInflater()`：调用`Policy.makeNewLayoutInflater()`

### Policy
`makeNewWindow()`：`new PhoneWindow(context)`，5.1中window的唯一初始化方式

* `makeNewLayoutInflater()`：`new PhoneLayoutInflater(context)`，5.1中layoutInflater唯二初始化地方

### WindowManagerImpl&WindowManagerGlobal
`WindowManagerImpl`为`WindowManager`的实现类，而`WindowManager`又是`ViewManager`的实现接口。`WindowManagerImpl`内部方法实现都是由代理类`WindowManagerGlobal`完成，而`WindowManagerGlobal`是一个单例，也就是一个进程中只有一个`WindowManagerGlobal`对象服务于所有页面的View。

```java
public final class WindowManagerGlobal {
    // 所有Window对象中的View
    private final ArrayList<View> mViews = new ArrayList<View>();
    // 所有Window对象中的View所对应的ViewRootImpl
    private final ArrayList<ViewRootImpl> mRoots = new ArrayList<ViewRootImpl>();
    // 所有Window对象中的View所对应的布局参数
    private final ArrayList<WindowManager.LayoutParams> mParams = new ArrayList<WindowManager.LayoutParams>();
}
```

### ViewRootImpl
> ViewRoot是GUI管理系统与GUI呈现系统之间的桥梁。

<img src="https://github.com/part5/note/raw/master/pic/ViewRootImpl.png" style="zoom: 200%;" />

`ViewRootImpl`是`View`中的最高层级，属于所有`View`的根（但`ViewRootImpl`不是`View`，只是实现了`ViewParent`接口），实现了`View`和`WindowManager`之间的通信协议，实现的具体细节在`WindowManagerGlobal`这个类当中。通过`ViewRootImpl`来更新界面并完成`Window`的添加过程。

##### ViewRootImpl的初始化
在`Activity`回调`onResume()`时，会调用`ActivityThread.handleResumeActivity()`，通过activity实例获取`WindowManager`实例，将`PhoneWindow`中的`DecorView`通过`WindowManager.addView()`添加到activity，内部实际是调用了`WindowManagerGlobal`的`addView()`，在`addView()`中创建`ViewRootImpl`实例，并将`ViewRootImpl`、`View`、`LayoutParams`实例存入集合。

* 里面有`ViewRootHandler`、`SyntheticJoystickHandler`、`SyntheticTouchNavigationHandler`这3个Handler
* performTraversals：在此方法中完成对View的MLD
* 

### DecorView
> 界面的根View，PhoneWindow的内部类。

### 注意
* APP中有几个进程，Application会被创建几次
* 新进程中所有变量和单例会失效，因为新进程有一块新的内存区域

### 最后
* [Activity中的Window的setContentView](http://dandanlove.com/2017/11/10/activity-setcontentview/)
* [遇见LayoutInflater和Factory](http://dandanlove.com/2017/11/15/layoutinflater-factory/)
* [ViewRootImpl的独白，我不是一个View](http://dandanlove.com/2017/12/11/viewrootimpl-activity/)
* [Dialog、Toast的Window和ViewRootImpl](http://dandanlove.com/2017/12/11/viewrootimpl-dialog-toast/)

