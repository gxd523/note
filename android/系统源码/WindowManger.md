> WindowManger是普通App进程用来与系统服务（WindowMangerService）通信的一个接口。

### 获取WindowManger实例
![](https://raw.githubusercontent.com/part5/note/master/pic/WindowManagerService.png)
![](https://raw.githubusercontent.com/part5/note/master/pic/WindowManager.png)

```java
WindowManager windowManager = (WindowManager) getSystemService(Context.WINDOW_SERVICE);
```

### 权限要求
```xml
<uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW"/>
```

### WindowManger添加view
> 注意 同一个window，只能添加一个View,添加多个View会报出以下异常
```java
windowManager.addView(view, params);
```

### WindowManger移除view
```java
windowManager.removeView(view, params);
```

### 控制响应事件
设置params的type：
	* FLAG_NOT_TOUCH_MODAL：
	* FLAG_NOT_TOUCHABLE：

### 控制显示层级
设置params的type：
	* TYPE_TOAST（Andorid 5.0及以下系统版本可规避权限问题）
	* TYPE_APPLICATION_WINOW （应用层Winow等级）
	* TYPE_PRIORTY_PHONE （系统层Window等级）

### 控制view的显示范围
设置params的flags：
	* FLAG_FULLSCREEN
	* FLAG_LAYOUT_IN_SCREEN5
