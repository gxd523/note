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

### Android中为什么主线程不会因为Looper.loop()里的死循环卡死？
