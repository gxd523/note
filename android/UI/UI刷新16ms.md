[TOC]

![](https://raw.githubusercontent.com/gxd523/PictureBed/master/ui_refresh.png)

## 前言
* 一个典型的显示系统包括CPU、GPU、Display三部分
* CPU负责计算数据，然后交给GPU
* GPU对图形数据进行渲染，渲染好后放到buffer里存起来
* Display每隔一段时间去buffer里取数据，并显示到屏幕
* Display读取buffer的频率是固定的
* CPU、GPU往buffer里写数据是无规律的
* 界面没变化，CPU不用计算新的数据，GPU也不用渲染，也不会往buffer中添加新的数据
* CPU的计算时长，是由布局的复杂程度(深度)，控件的数量等决定的
* CPU绘制视图树来计算下一帧数据是从收到屏幕刷新信号开始的
* CPU计算好数据，GPU渲染完放入buffer后，在下一个屏幕刷新信号Display才读取显示

## 从View刷新开始
* 刷新UI操作包括：`invalidate()`、`requestLayout()`、`reqeustFocus()`、`Anination`、`KeyEvent`、`startAcivity()`...
* 这些刷新操作都会通过调用`mParent`的相关方法，不断往上，最终到达`ViewRootImpl.scheduleTraversals()`
* 里面会调用`Choreographer.postCallback()`，并传入执行`performTraversals()`方法的`Runnable`，此时发送同步屏障
* 里面将`Runnable`放入`CallbackQueue`，并调用`Choreographer.scheduleFrameLocked()`
* 如果当前在主线程，执行`Choreographer.scheduleVsyncLocked()`，否则发送插到`MessageQueue`头部，执行的`Message`，来切回主线程，还是执行`scheduleVsyncLocked()`
* `Choreographer.scheduleVsyncLocked()`：里面向native层注册一个`DisplayEventReceiver`监听，注意一次注册只能监听下一次的屏幕刷新信号
* 当底层每隔16ms发送屏幕刷新信号时，如果注册了监听就会回调`DisplayEventReceiver.onVsync()`方法
* `onVsync()`方法内会调用`doFrame()`，然后从`CallbackQueue`中拿到我们刚才的`Runnable.run()`

## 问答
### 我们都知道Android的刷新频率是60帧/秒，这是不是意味着每隔16ms就会调用一次onDraw方法？
> Display每隔16ms读取一次buffer，这是固定不变的，但是是否会调用onDraw()方法要看应用是否调用刷新UI的方法。

### 如果界面没变，那么还16ms到后还会刷新屏幕吗？
> 界面没变，意味着没有调用刷新UI的相关方法，也就不会注册屏幕刷新信号的监听，那么应用就不会受到Vsync信号，也不会执行`performTraversals()`去刷新UI

### 界面的显示其实就是一个 Activity 的 View 树里所有的 View 都进行测量、布局、绘制操作之后的结果呈现，那么如果这部分工作都完成后，屏幕会马上就刷新么？
> 这部分工作完成，意味着，上一帧时注册了屏幕刷新信号监听，并在屏幕信号刷新时执行了MLD的操作，之后会交由GPU渲染让如buffer，然后等到下一个Vsync时，Display才会取buffer显示到屏幕

### 我们说丢帧是因为主线程做了耗时操作，为什么主线程做了耗时操作就会引起丢帧
> 丢帧有两大原因，一个是View绘制超过了16ms，另一个是主线程在耗时操作，迟迟没法开始View的绘制，虽然加入了同步屏障，但只能屏蔽同步屏障之后的同步消息，如果在之前有个耗时操作，导致后面的调用`performTraversals()`的`Message`超过16ms都得不到执行

#### 如果在屏幕快要刷新的时候才去OnDraw()绘制，会丢帧吗？
> 当这个Vsync到来时，才回去执行`performTraversals()`，去做CPU的计算绘制的工作，正常情况下，在下一个Vsync到来前，完成GPU渲染，放入buffer，下一个Vsync到来时，才会显示刚才的UI更新。虽然从调用到显示隔了一个Vsync，但一系列的刷新是连贯的，所以不会丢帧。

