[TOC]

## 协程
* 协程是一个`线程框架`，和`RxJava`、`Handler`、`AsyncTask`一样，都是在`Thread`基础上封装的`线程框架`
* 协程的核心竞争力：
	* 用`同步方式`(返回值)写`异步代码`(回调)，简化异步逻辑，避免`回调地狱`(消除嵌套)
	* 用更少的线程做尽量多的任务
* 协程的本质：`CPS`+`状态机`

## 挂起函数
* `挂起函数`挂起的不是线程，而是代码逻辑，即`挂起函数`本身
* 线程只会被操作系统挂起，线程的执行对我们来说是透明的

### 挂起&恢复
* `挂起函数`具有`挂起(暂停)`、`恢复`的能力
* `挂起(suspend)`：从当前线程切换到其他线程，执行`suspend函数`中的代码
* `恢复(resume)`：从其他线程切回当前线程，返回结果
* `挂起(suspend)`是`非阻塞`的：切换到其他线程时，也不会阻塞当前线程

### Continuation
* `suspend`关键字的本质就是`Callback`
* `Continuation`就是所谓的`Callback`，`resumeWith(Result<T>)`即回调函数
* `Continuation`表示接下来要执行的代码，或者剩下的代码