
[TOC]

## WindowManager
* `WindowManager`时AIDL接口
* `WindowManagerImpl`：
	* 实现`WindowManager`接口的客户端代理
	* 存在多个实例，每个`Window`对应一个
* `WindowManagerService`：实现`WindowManager`接口的服务端
* `WindowManagerGlobal`：
	* `WindowManagerImpl`功能的具体实现
	* 进程中单一实例


> WindowManger是普通App进程用来与系统服务（WindowMangerService）通信的一个接口。

### 获取WindowManger实例

```java
WindowManager windowManager = (WindowManager) getSystemService(Context.WINDOW_SERVICE);
```

### 权限要求
```xml
<uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW"/>
```

### WindowManger添加view
> 注意 同一个window，只能添加一个View,添加多个View会报出以下异常
```kotlin
windowManager.addView(
    ImageView(this).also {
        Glide.with(it).load(R.mipmap.ic_launcher).into(it)
    },
    WindowManager.LayoutParams().apply {
        width = 300
        height = 300
        type = WindowManager.LayoutParams.TYPE_APPLICATION_OVERLAY
    }
)
```

### WindowManger移除view
```java
windowManager.removeView(view, params);
```

### 控制响应事件
设置params的type：
	* FLAG_NOT_TOUCH_MODAL：
	* FLAG_NOT_TOUCHABLE：

### Window分类
Window 类型 | 说明 | 层级
:---: | :---: | :---:
Application Window | 对应着一个 Activity | 1~99
Sub Window | 不能单独存在，只能附属在父 Window 中，如 Dialog 等 | 1000~1999
System Window | 需要权限声明，如 Toast 和 系统状态栏等 | 2000~2999

### 控制显示层级
设置params的type：
	* TYPE_TOAST（Andorid 5.0及以下系统版本可规避权限问题）
	* TYPE_APPLICATION_WINOW （应用层Winow等级）
	* TYPE_PRIORTY_PHONE （系统层Window等级）

### 控制view的显示范围
设置params的flags：
	* FLAG_FULLSCREEN
	* FLAG_LAYOUT_IN_SCREEN5
