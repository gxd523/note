### 线程状态
* NEW
> 一个尚未启动的线程处于这一状态。

A thread that has not yet started is in this state.

* RUNNABLE
> 一个正在 Java 虚拟机中执行的线程处于这一状态。
A thread executing in the Java virtual machine is in this state.

* BLOCKED
> 一个正在阻塞等待一个监视器锁的线程处于这一状态。

A thread that is blocked waiting for a monitor lock is in this state.

* WAITING
> 一个正在无限期等待另一个线程执行一个特别的动作的线程处于这一状态。

A thread that is waiting indefinitely for another thread to perform a particular action is in this state.
* TIMED_WAITING(计时等待)
> 一个正在限时等待另一个线程执行一个动作的线程处于这一状态。

A thread that is waiting for another thread to perform an action for up to a specified waiting time is in this state.

* TERMINATED
> 一个已经退出的线程处于这一状态。

A thread that has exited is in this state.

