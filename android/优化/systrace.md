[TOC]

* systrace是一个python脚本，轻量级，开销小
* w：放大；s：缩小；A：左平移；<-：上个事件；M：选中
* Wall Duration：实际执行事件；CPU Duration(更具参考意义)：CPU执行时间
* Wall Time>CPU Time可能是锁冲突之类的情况
* `$ANDROID_SDK_HOME/platform-tools/systrace/systrace.py -t 5 -o Desktop/a.html -a com.yangqi.rom.launcher.free gfx view wm am`

## 操作
* w：放大，s：缩小，a：左移，d：右移，m：高亮选中，f：放大选中区域
* v：高亮VSync所在的一帧，g：帧分割线，0：恢复初始状态
* 灰色： 睡眠
* 蓝色： 可以运行（它可以运行，但还未被调度运行）
* 绿色： 正在运行（调度程序认为它正在运行）
* 红色： 不间断的睡眠（通常发生在内核锁上）， 指出I / O负载，对于性能问题的调试非常有用
* 橙色： 由于I / O负载导致的不间断睡眠
* 圆圈F：如果不是绿色都说明UI渲染时间超过一帧

## debuggable模式
* debuggalbe模式的app性能更差，为了更准确模拟线上，应使用非debuggable
* 非debuggable模式需要反射开启：
```java
Class<?> trace = Class.forName("android.os.Trace");
Method setAppTracingAllowed = trace.getDeclaredMethod("setAppTracingAllowed", boolean.class);
setAppTracingAllowed.invoke(null, true);
```

## Trace Lable
* `Trace.beginSection(LABEL_NAME)`
* `Trace.endSection()` 

## option
* `-a <PACKAGE_NAME>`：`--app=<PACKAGE_NAME>`
* `-o <Desktop/a.html>`
* `-t <秒>`：`--time=<秒>`
* `-l`：`--list-categories`，显示所有category
* -b：用于限制trace总大小，默认无上限，单位kb

## category
category | 含义
:---: | :--:
gfx | Graphics
input | Input
view | View System
webview | WebView
wm | Window Manager
am | Activity Manager
sm | Sync Manager
audio | Audio
video | Video
camera | Camera
hal | Hardware Modules
res | Resource Loading
dalvik | Dalvik VM
rs | RenderScript
bionic | Bionic C Library
power | Power Management
pm | Package Manager
ss | System Server
database | Database
network | Network
adb | ADB
vibrator | Vibrator
aidl | AIDL calls
nnapi | NNAPI
rro | Runtime Resource Overlay
pdx | PDX services
sched | CPU Scheduling
irq | IRQ Events
i2c | I2C Events
freq | CPU Frequency
idle | CPU Idle
disk | Disk I/O
sync | Synchronization
workq | Kernel Workqueues
memreclaim | Kernel Memory Reclaim
binder_driver | Binder Kernel driver
binder_lock | Binder global lock trace
pagecache | Page cache

## Android9(28)
* 开发者模式，`System Tracing`，`Show Quick Settings tile`
* 在`data/local/traces`下找到文件
* 将文件转为HTML：`./systrace.py --from-file /Users/guoxiaodong/Desktop/aaa.perfetto-trace -o /Users/guoxiaodong/Desktop/bbb.html`