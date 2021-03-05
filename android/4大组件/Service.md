[TOC]

![](https://gitee.com/hysbtr/pic/raw/master/service_lifecycle.png)

## Service两种启动方式
* 同时使用`starService()`和`bindService()`，需要`stopService()`、`unbindService()`都调用才能销毁

### starService()启动方式
* 生命周期独立于启动它的组件
* service内部通过stopSelf()停止服务，外部用启动service的组件的stopService()停止服务
* 生命周期方法：`onCreate()`-->`onStartCommand()`-->`onDestroy()`
* 多次`startService()`会多次回调`onStartCommand()`，可用于`IntentService`添加任务

#### onStartCommand()的返回值
##### START_STICKY
* 如果此服务的进程在启动时(onStartCommand()之后)被终止
* 保持started状态，但不保留Intent
* 稍后系统将尝试重新创建服务
* 由于处于started状态，所以一定调用onStartCommand()，Intent为null

##### START_NOT_STICKY
* 如果此服务的进程在启动时(onStartCommand()之后)被终止
* 系统不会重新创建服务

 ##### START_REDELIVER_INTENT
* 如果此服务的进程在启动时(onStartCommand()之后)被终止
* 保持started状态，保留Intent
* 稍后系统将尝试重新创建服务
* 由于处于started状态，所以一定调用onStartCommand()，Intent不为null

 ##### START_STICKY_COMPATIBILITY
* START_STICKY的兼容版本，但是不能保证被清理后onStartCommand方法一定会被重新调用

### bindService()启动方式
* 组件调用`unbindService()`停止服务
* 多个组件绑定服务时，当所有组件都销毁时，服务才会停止
* 多个组件绑定服务时，只有第一个组件绑定后会回调`onCreate()`、`onBind()`
* 生命周期方法：`onCreate()`-->`onBind()`-->`onUnbind()`-->`onDestroy()`
* 多次`bindservice()`，`onBind()`只会回调一次

## IntentService
> IntentService 是Service 的子类，它使用工作线程逐一处理所有启动请求，如果不要求服务同时处理多个请求，这是最好的选择。

* 优先级高
* 串行执行异步任务
* 全部任务完成后自我销毁
* 注意stopSelf(startId)里传入的startId对应最后一次调用onStartCommand的startId才会销毁service

## 前台Service
### 服务保活
* 提高Service优先级：`android:priority="1000"`
* 把Service写成系统服务，在清单文件设置：`android:persistent="true"`，不建议
* 将Service设置为前台Service
```xml
<uses-permission android:name="android.permission.FOREGROUND_SERVICE" />
```
```java
public int onStartCommand(Intent intent, int flags, int startId) {
    String channelId = "YOUR_CHANNEL_ID001";
    Notification.Builder builder;
    if (android.os.Build.VERSION.SDK_INT >= android.os.Build.VERSION_CODES.O) {
        builder = new Notification.Builder(this.getApplicationContext(), channelId);
    } else {
        builder = new Notification.Builder(this.getApplicationContext());
    }

    PendingIntent pendingIntent = PendingIntent.getActivity(this, 0, new Intent(this, MainActivity.class), 0);
    builder.setContentIntent(pendingIntent) // 设置PendingIntent
            .setLargeIcon(BitmapFactory.decodeResource(this.getResources(), R.drawable.zzhx)) // 设置下拉列表中的图标(大图标)
            .setContentTitle("下拉列表中的Title") // 设置下拉列表里的标题
            .setSmallIcon(R.drawable.ccdzz) // 设置状态栏内的小图标
            .setContentText("要显示的内容") // 设置上下文内容
            .setWhen(System.currentTimeMillis()) // 设置该通知发生的时间
            .setDefaults(Notification.DEFAULT_SOUND);// 设置为默认的声音

    if (android.os.Build.VERSION.SDK_INT >= android.os.Build.VERSION_CODES.O) {
        NotificationManager notificationManager = (NotificationManager) getSystemService(Service.NOTIFICATION_SERVICE);
        NotificationChannel channel = new NotificationChannel(channelId, "YOUR_CHANNEL_NAME", NotificationManager.IMPORTANCE_HIGH);
        notificationManager.createNotificationChannel(channel);
    }

    startForeground(111, builder.build());
    return super.onStartCommand(intent, flags, startId);
}
```
* 利用系统广播，`Intent.ACTION_TIME_TICK`每分钟发送一次，检查`Servce`是否存活

