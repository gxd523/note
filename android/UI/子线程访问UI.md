## 子线程能否更新UI
* 可以在子线程中更新UI，例如Dialog、Toast，但必须在子线程中创建Dialog、Toast
* 创建`ViewRootImpl`实例时，会记录当前线程`mThread = Thread.currentThread()`
* `ViewRootImpl.checkThread()`会在`mThread != Thread.currentThread()`时抛异常
* 所以只要保证`ViewRootImpl`创建，和`ViewRootImpl.checkThread()`(一般UI相关操作时会检查)，在同一个线程即可

## 为啥不建议子线程中访问UI
* UI线程是不安全的，没加锁机制的原因：
	* 锁机制让UI访问逻辑变复杂
	* 锁机制降低UI访问效率，可造成阻塞问题
* 所以Android采用单线程模型处理UI
* 