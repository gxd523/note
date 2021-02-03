
[TOC]

## Message
* 通过`Message`类型的`next`属性指向下一个`Message`，实现`单向链表`结构
* 通过静态`Message`类型的`sPool`属性作为头结点，实现可回收复用的`Message`池
* 取出`Message`后，需要把`next`属性置为`null`，从`单向链表`中断开

## MessageQueue
* `enqueueMessage()`：同步代码块中，`msg`与头结点`mMessages`比较时间`when`
	* 如果`msg.when`更小，头结点指向`msg`
	* 如果`msg.when`更大，遍历链表`mMessages`，找到链表`when`<`msg.when`的元素前插入`msg`
	* `nativeWake()`唤醒队列取消息
* `next()`：`for`死循环中，取头结点`msg`，与当前时间比较
	* now>=`msg.when`，`msg.next = null`，返回`msg`
	* now<`msg.when`：
		* `idleHandlerCount`>0：遍历集合执行` idler.queueIdle()`
		* `idleHandlerCount`为0：`nativePollOnce(now - msg.when)`

### IdleHandler
* 通过`MessageQueue.addIdleHandler()`添加
* 在`MessageQueue.next()`中，如果没有消息，或者下一个消息延迟执行，就会回调IdleHandler
* 可用来获取View宽高，MessageQueue空闲时，说明ViewRootImpl的MLD已经走完了
* 不要在里面执行耗时操作，因为在主线程
* 在`OnResume()`里面调用获取到MainActivity的绘制完成的时机

### 同步屏障(SyncBarrier)
* 通过`postSyncBarrier()`插入`MessageQueue`的消息
* `同步屏障`和普通消息的区别是`target`为空，`同步屏障`不需要被`Handler`处理
* `同步屏障`的`arg1`会被设置`token`
* `removeSyncBarrier(token)`：通过`token`删除`同步屏障`
* 在`next()`方法中，如果头结点`target`==null，即`同步屏障`，会跳过之后的普通消息，直到找到`异步消息`
* `异步消息`是设置了`msg.setAsynchronous()`

```java
HandlerThread handlerThread = new HandlerThread("test");
handlerThread.start();
handler = new Handler(handlerThread.getLooper()) {
    @Override
    public void handleMessage(@NonNull Message msg) {
        Log.d("gxd", "收到..." + msg.obj);
    }
};

/**
 * 往消息队列插入同步屏障
 */
public void sendSyncBarrier(View view) throws Exception {
    Log.d("gxd", "插入同步屏障");
    MessageQueue queue = handler.getLooper().getQueue();
    Method method = MessageQueue.class.getDeclaredMethod("postSyncBarrier");
    method.setAccessible(true);
    token = (int) method.invoke(queue);
}

/**
 * 移除同步屏障
 */
public void removeSyncBarrier(View view) throws Exception {
    Log.d("gxd", "移除屏障");
    MessageQueue queue = handler.getLooper().getQueue();
    Method method = MessageQueue.class.getDeclaredMethod("removeSyncBarrier", int.class);
    method.setAccessible(true);
    method.invoke(queue, token);
}

/**
 * 往消息队列插入普通消息
 */
public void sendSyncMessage(View view) {
    Log.d("gxd", "插入普通消息");
    Message message = Message.obtain();
    message.obj = "同步消息";
    handler.sendMessageDelayed(message, 1000);
}

/**
 * 往消息队列插入异步消息
 */
public void sendAsyncMessage(View view) {
    Log.d("gxd", "插入异步消息");
    Message message = Message.obtain();
    message.obj = "异步消息";
    message.setAsynchronous(true);
    handler.sendMessageDelayed(message, 1000);
}
```

## Looper
* 通过静态方法`prepare()`创建`Looper`实例，并放进`ThreadLocal`对象中
* `MessageQueue`在`Looper`构造时作为属性一同构造，一个`Looper`对应一个`MessageQueue`
* `ThreadLocal`：
	* `Thread`中有一个类似`Map`的变量，Key为`ThreadLocal`，Value为`Looper`
	* `threadLocal.get()`会从当前线程的`Map`中拿Key为`threadLocal`的Value
	* `ThreadLocal`是线程的私有内存
* `loop()`：死循环中从`MessageQueue.next()`拿`Message`，调用`msg.target.dispatchMessage()`分发消息
* 注意：没有消息了，`MessageQueue.next()`会阻塞，`MessageQueue.quit()`才会返回null，退出`loop()`死循环

#### Looper.loop()
```java
public static void loop() {
    final Looper me = myLooper();
    if (me == null) {
        throw new RuntimeException("No Looper; Looper.prepare() wasn't called on this thread.");
    }
    final MessageQueue queue = me.mQueue;

    for (; ; ) {
        Message msg = queue.next(); // might block
        if (msg == null) {
            // No message indicates that the message queue is quitting.
            return;
        }
    }

    // ....
}
```
## Handler
* `dispatchMessage()`：在`Looper.loop()`中拿到消息后分发消息
* `sendMessageAtTime()`：延迟、空或者普通消息发送时都会走到这
* `handleMessage()`：处理消息

## 问题
### Handler如何处理延迟消息
> 通过`sendMessageAtTime()`把延迟转化为具体时间点，然后插入单链表，取出时通过`nativePollOnce()`休眠到时间点后，分发处理消息

### View.post 和 Handler.post 的区别
* `View.post()`通过`attachInfo.mHandler.post()`向`Handler`发消息
* 在`ViewRootImpl`构造函数里创建`AttachInfo`实例
* `AttachInfo.mHandler`是`ViewRootImpl.ViewRootHandler`
* 通过`View.dispatchAttachedToWindow()`，将`AttachInfo`对象传给`View`
* 如果在`ViewRootImpl.performTraversal()`前调用`View.post()`，先把`Runnable`保存在数组里，等`View.dispatchAttachedToWindow()`时再遍历`Runnable`数组，调用`Handler.post()`

### Looper.loop()的死循环会导致应用卡死吗？
* Android是基于事件驱动的，交互操作，刷新UI、页面跳转等等，都是通过发送`Message`来完成的
* 线程任务执行完，线程便会结束，死循环可以不让UI线程在没有Message时就结束

### Looper.loop()的死循环会不会很耗CPU资源
* 没有消息时，会阻塞在`MessageQueue.next()`里的Native方法`nativePollOnce()`中
* `nativePollOnce()`里涉及到Linux的`pipe/epoll`机制，是一种`IO多路复用机制`
* `nativePollOnce()`后主线程会释放CPU资源进入休眠状态
* 调用`Message.enqueueMessage()`时，会往`pipe管道`写端写入数据，唤醒主线程
