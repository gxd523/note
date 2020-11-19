
## ANR常见原因
* 主线程执行耗时操作，如I/O操作，等导致Input无法在5s内得到相应
* 主线程阻塞，例如同步代码块
* `ContentProvider`方法执行超时10s
* CPU负载过高，无法解决

### Service
* 当Service生命周期开始时，发送延迟消息，延迟时间：前台进程执行的Service为20秒，后台进程执行的Service为200秒
* 生命周期结束时，会清除延迟消息

### BroadcastReceiver
* `onReceive()`在前台进程执行不超过10s，后台进程不能超过60s

### Input处理超时
* 触摸、按键等`InputEvent`超过5s没响应


## 查找ANR方法
### StrictMode
### 启动ANR对话框
### 使用TraceView
### adb pull data/anr

## ANR日志
* `ANR in <进程名称(applicationId)>`
* `PID`：进程id
* `Reason`：原因
* `Load: <a> / <b> / <c>`：a、b、c分别表示最近1分钟、5分钟、15分钟的CPU负载，最近1分钟最具参考价值
* `CPU usage from <a>ms to <b>ms ago/after`：a表示

## trace文件
* 日志搜索`Dumping to `，找到tace文件路径
* 

## 其他
* `Input dispatching timed out (Waiting because no window has focus but there is a focused application that may eventually add a window when it finishes starting up.`
* 
1. 输入事件(按键和触摸事件)5s内没被处理: Input event dispatching timed out
2. BroadcastReceiver(onRecieve方法)在规定时间内完成执行(前台广播为10s，后台广播为60s): Timeout of broadcast BroadcastRecord
3. 

