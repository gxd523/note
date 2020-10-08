![](https://gitee.com/hysbtr/pic/raw/master/service_lifecycle.png)

### Service两种启动方式
#### starService()启动方式
* 生命周期独立于启动它的组件
* service内部通过stopSelf()停止服务，外部用启动service的组件的stopService()停止服务
* 生命周期方法：`onCreate()`-->`onStartCommand()`-->`onDestroy()`

#### bindService()启动方式
* 组件调用`unbindService()`停止服务
* 多个组件绑定服务时，当所有组件都销毁时，服务才会停止
* 多个组件绑定服务时，只有第一个组件绑定后会回调`onCreate()`、`onBind()`
* 生命周期方法：`onCreate()`-->`onBind()`-->`onUnbind()`-->`onDestroy()`

### IntentService
> IntentService 是Service 的子类，它使用工作线程逐一处理所有启动请求，如果不要求服务同时处理多个请求，这是最好的选择。

* 执行一些高优先级的后台任务。由于属于Service，若以比单纯的线程优先级更高。
* 任务一个一个执行

### Broadcast
* 清单文件的广播接收者是在手机启动的时候就注册了，或者是应用安装完成后
* 