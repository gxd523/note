### 哪些情况下会出现ANR
* `Input dispatching timed out (Waiting because no window has focus but there is a focused application that may eventually add a window when it finishes starting up.`
* 
1. 输入事件(按键和触摸事件)5s内没被处理: Input event dispatching timed out
2. BroadcastReceiver(onRecieve方法)在规定时间内完成执行(前台广播为10s，后台广播为60s): Timeout of broadcast BroadcastRecord
3. 

