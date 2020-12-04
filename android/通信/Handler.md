
[TOC]

### Handler
* 重要对象：looper、messageQueue
#### sendMessageDelayed()
#### dispatchMessage()
#### handleMessage()

### Looper
#### threadLocal
> 每个线程独立的存储空间，可以看成Map<Thread,Looper>。

#### messageQueue
> 存储消息的队列

#### prepare()
> 实例化Looper，messageQueue是在Looper构造函数中实例化的。

#### looper()
> 无限for循环中，从messageQueue.next()中取msg，调用msg.target.dispatchMessage()分发消息

### MessageQueue
* 重要对象：delayQueue
* 重要方法：enqueueMessage()、next()
* 采用单链表数据结构，Message持有下一个Message的引用
### Message

* 在CPU看来进程或线程无非就是一段可执行的代码，当可执行代码执行完成后，线程就会结束退出
* 为了保证程序不退出，简单做法就是执行死循环代码，让程序无法执行完。
* 死循环带来两个问题，一个是占用CPU资源，另一个是处理其他任务
* 死循环不会造成ANR，或线程阻塞，

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

#### ActivityThread.main()
```java
public static void main(String[] args) {
    Looper.prepareMainLooper();

    ActivityThread thread = new ActivityThread();
    // 建立Binder通道(创建新线程)
    thread.attach(false);

    Looper.loop();

    throw new RuntimeException("Main thread loop unexpectedly exited");
}
```

## Looper.loop()的死循环会导致应用卡死吗？
* 线程任务执行完，线程便会结束，死循环可以不让UI线程在没有Message时就结束
* Android是基于事件驱动的，所有操作都是Message，所以当用户触摸屏幕时，就会向Handler发送消息，没有消息时，会进入休眠状态

## Looper.loop()的死循环会不会很耗CPU资源
* 没有消息时，会阻塞在`MessageQueue.next()`里的Native方法`nativePollOnce()`中
* `nativePollOnce()`里涉及到Linux的`pipe/epoll`机制，是一种`IO多路复用机制`
* `nativePollOnce()`后主线程会释放CPU资源进入休眠状态
* 调用`Message.enqueueMessage()`时，会往`pipe管道`写端写入数据，唤醒主线程

## 进阶
### 消息分类
#### 同步消息(普通消息)
#### 同步屏障(屏障消息)
* 在消息队列中插入一个屏障，阻挡后面的普通消息分发，保证异步消息的优先级
* 和普通消息一样可以根据时间插入到队列的任意位置
* 没有target，因为不需要分发

#### 异步消息
* 通过隐藏的Handler构造函数创建的Handler可发送异步消息，或者`message.setAsynchronous()`直接设置消息为异步消息

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

### IdleHandler
* 通过`MessageQueue.addIdleHandler()`添加
* 在`MessageQueue.next()`中，如果没有消息，或者下一个消息延迟执行，就会回调IdleHandler
* 可用来获取View宽高，MessageQueue空闲时，说明ViewRootImpl的MLD已经走完了
* 不要在里面执行耗时操作，因为在主线程
* 在`OnResume()`里面调用获取到MainActivity的绘制完成的时机

## View.post()&Handler.post()
* `View.post()`通过`attachInfo.mHandler.post()`向`Handler`发消息
* 在`ViewRootImpl`构造函数里创建`AttachInfo`实例
* `AttachInfo.mHandler`是`ViewRootImpl.ViewRootHandler`
* 通过`View.dispatchAttachedToWindow()`，将`AttachInfo`对象传给`View`
* 如果在`ViewRootImpl.performTraversal()`前调用`View.post()`，先把`Runnable`保存在数组里，等`View.dispatchAttachedToWindow()`时再遍历`Runnable`数组，调用`Handler.post()`
