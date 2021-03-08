[TOC]

## ANR
> Application Not Responding

## ANR的类型
* KeyDispatchTimeout：按键或触摸事件，5s内无响应
* BroadcastTimeout：BroadcastReceiver的onReceive()在10s内未执行完
* ServiceTimeout：Service的生命周期方法在20s内未执行完

## 超时原因
* 当前事件没有机会得到处理，即UI线程被占用
* 当前事件在UI线程处理超时

## Logcat日志分析
* trace文件路径：`Dumping to /data/anr/anr_2021-03-07-19-34-59`
* ANR发生的应用：`ANR in com.github (com.github/.OkHttpActivity)`
* ANR发生的进程id：`PID: 15782`
* ANR发生的原因：`Reason: Input dispatching timed out`
* 5、10、15分钟的平均负载：`Load: 0.55 / 0.38 / 0.22`
* CPU使用百分比，用户、内核、I/O操作分别占用多少：`11% TOTAL: 3.5% user + 7.8% kernel + 0% iowait + 0% softirq`

## trace文件
* trace文件路径：`Dumping to /data/anr/anr_2021-03-07-19-34-59`
* 缩小查找范围：根据PID、包名查找



## ANR处理
主线程阻塞的
开辟单独的子线程来处理耗时阻塞事务.

CPU满负荷, I/O阻塞的
I/O阻塞一般来说就是文件读写或数据库操作执行在主线程了, 也可以通过开辟子线程的方式异步执行.

内存不够用的
增大VM内存, 使用largeHeap属性, 排查内存泄露(这个在内存优化那篇细说吧)等.


## 查找ANR方法
### StrictMode
### 启动ANR对话框
### 使用TraceView
### adb pull data/anr
