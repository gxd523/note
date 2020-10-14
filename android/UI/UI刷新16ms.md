![](https://gitee.com/hysbtr/pic/raw/master/ui_refresh.png)

#### 我们都知道Android的刷新频率是60帧/秒，这是不是意味着每隔16ms就会调用一次onDraw方法？
> 这里60帧/秒是屏幕刷新频率，但是是否会调用onDraw()方法要看应用是否调用requestLayout()进行注册监听。

#### 如果界面不需要重绘，那么还16ms到后还会刷新屏幕吗？
> 如果不需要重绘，那么应用就不会受到Vsync信号，但是还是会进行刷新，只不过绘制的数据不变而已；

#### 我们调用invalidate()之后会马上进行屏幕刷新吗？
> 不会，到等到下一个Vsync信号到来

#### 我们说丢帧是因为主线程做了耗时操作，为什么主线程做了耗时操作就会引起丢帧
> 原因是，如果在主线程做了耗时操作，就会影响下一帧的绘制，导致界面无法在这个Vsync时间进行刷新，导致丢帧了。

#### 如果在屏幕快要刷新的时候才去OnDraw()绘制，会丢帧吗？
> 这个没有太大关系，因为Vsync信号是周期的，我们什么时候发起onDraw()不会影响界面刷新；

### Frame Buffer
> GPU的一块缓存区，是一个数组，数组中的元素代表像素点的颜色。手机逻辑电路会定期显示Frame Buffer里面的数据，例如60Hz，就是每16.7ms刷新一次。

### Back Buffer
> GPU的另一个缓存区，CPU将显示数据写入Back Buffer里，GPU定期对Back Buffer进行栅格化处理，也就是转换成Frame Buffer。

### VSYNC(Vertical Synchronization)
> 定时中断

### Triple Buffer
> 添加第三个Buffer，防止前两个Buffer分别被Display、GPU占用，导致CPU没有Buffer处理

### Choreographer

