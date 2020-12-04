[TOC]

## 注册方式
### 静态注册
* 即使按返回键退出App，变成空进程，也依然能接收广播
* 注意Android8.0(26)开始，无法静态注册广播接收者，除了以下必要广播：
	* ACTION_BOOT_COMPLETED
	* ACTION_TIME_SET
	* ACTION_LOCALE_CHANGED
* Android8.0(26)开始，需要发送广播时指定静态接收者，`intent.setComponent()`，才能静态接收到

### 动态注册
* 需要在`onDestroy()`中注销

## 广播类型
### 普通广播（Normal Broadcast）
* 开发者定义的Intent广播

### 系统广播（System Broadcast）
* Android系统内置的系统广播，如开机、网络变化、拍照等
* 每个广播都有特定的`action`：
系统操作 | action
--- | ---
监听网络变化 | android.net.conn.CONNECTIVITY_CHANGE
关闭或打开飞行模式 | Intent.ACTION_AIRPLANE_MODE_CHANGED
充电时或电量发生变化 | Intent.ACTION_BATTERY_CHANGED
电池电量低 | Intent.ACTION_BATTERY_LOW
电池电量充足（即从电量低变化到饱满时会发出广播	 | Intent.ACTION_BATTERY_OKAY
系统启动完成后(仅广播一次) | Intent.ACTION_BOOT_COMPLETED
按下照相时的拍照按键(硬件按键)时 | Intent.ACTION_CAMERA_BUTTON
屏幕锁屏 | Intent.ACTION_CLOSE_SYSTEM_DIALOGS
设备当前设置被改变时(界面语言、设备方向等) | Intent.ACTION_CONFIGURATION_CHANGED
插入耳机时 | Intent.ACTION_HEADSET_PLUG
未正确移除SD卡但已取出来时(正确移除方法:设置–SD卡和设备内存–卸载SD卡) | Intent.ACTION_MEDIA_BAD_REMOVAL
插入外部储存装置（如SD卡） | Intent.ACTION_MEDIA_CHECKING
成功安装APK | Intent.ACTION_PACKAGE_ADDED
成功删除APK | Intent.ACTION_PACKAGE_REMOVED
重启设备 | Intent.ACTION_REBOOT
屏幕被关闭 | Intent.ACTION_SCREEN_OFF
屏幕被打开 | Intent.ACTION_SCREEN_ON
关闭系统时 | Intent.ACTION_SHUTDOWN
重启设备 | Intent.ACTION_REBOOT

### 有序广播（Ordered Broadcast）
* 多个广播接收者按照优先顺序接收广播
* 优先级`Priority`高的先接收，动态的优先于静态接收者
* 接收者可以截断广播：`abortBroadcast()`
* 接收者可以增加传递数据`setResultData()`

### 粘性广播（Sticky Broadcast）
* 需要申请权限`android.permission.BROADCAST_STICKY`
* 粘性广播的特点是Intent会一直保留到广播事件结束

### 本地广播（Local Broadcast）
* 使用`LocalBroadcastManager`发送广播、注册广播接收者
* 需要引入`androidx.localbroadcastmanager`