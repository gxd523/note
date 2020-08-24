[TOC]

### IPC方式
> Android系统是基于Linux开发的，Linux中包含以下几种IPC:

* **管道(PIPE)**：在创建时分配一个page大小的内存，缓存区大小比较有限；
* **消息队列**：信息复制两次，额外的CPU消耗；不合适频繁或信息量大的通信；
* **共享内存**：无须复制，共享缓冲区直接付附加到进程虚拟地址空间，速度快；但进程间的同步问题操作系统无法实现，必须各进程利用同步工具解决；
* **套接字**：作为更通用的接口，传输效率低，主要用于不通机器或跨网络的通信；
* **信号量**：常作为一种锁机制，防止某进程正在访问共享资源时，其他进程也访问该资源。因此，主要作为进程间以及同一进程内不同线程之间的同步手段。
* **信号**: 不适用于信息交换，更适用于进程中断控制，比如非法内存访问，杀死某个进程等；

### Android中的IPC方式
* ContentProvider
* Broadcast
* AIDL
* Socket
* Messager

#### 开启多进程的影响
* 静态成员与单例模式失效
* 线程同步机制失效
* SharedPreferences 可靠性下降
* Application 被创建多次

### AIDL(Android Interface Definition Language)
##### AIDL支持的数据类型
* 8种基本数据类型：byte、char、short、int、long、float、double、boolean
* String、CharSequence
* 实现了Parcelable接口的数据类型
* List 类型(List元素必须是AIDL支持的类型，或者是其它声明的AIDL对象)
* Map类型(Map键、值必须是AIDL支持的类型，或者是其它声明的AIDL对象)

##### AIDL文件分类
* 实现了Parcelable接口的数据类型声明
* 暴露方法的接口声明

##### 定向tag
* 定向Tag表示在跨进程通信中数据的流向，用于标注方法的参数值，分为 in、out、inout 三种。因为方法不能有返回值？
* `in`表示输入型参数：Server可以获取到Client传递过去的数据，但是不能对Client端的数据进行修改
* `out`表示输出型参数：Server获取不到Client传递过去的数据，但是能对Client端的数据进行修改
* `inout`表示输入输出型参数：Server可以获取到Client传递过去的数据，但是能对Client端的数据进行修改
* 如果AIDL方法接口的参数值类型是：基本数据类型、String、CharSequence或者其他AIDL文件定义的方法接口，那么这些参数值的定向 Tag 默认是且只能是 in，所以除了这些类型外，其他参数值都需要明确标注使用哪种定向Tag
* 在AIDL文件中需要明确标明引用到的数据类型所在的包名，即使两个文件处在同个包名下

![](https://github.com/part5/note/raw/master/pic/aidl.png)

![](https://github.com/part5/note/raw/master/pic/ipc-aidl.jpg)

#### AIDL的经典实现：Messenger
> Messenger可以翻译为信使，顾名思义，通过它可以在不同进程中传递Message对象，在Message中放入我们需要传递的数据，就可以轻松地实现数据的进程间传递了。Messenger是一种轻量级的IPC方案，它是AIDL在Android中的一种经典实践。

* Messenger 是以串行的方式处理客户端发送的消息，即使有大量的消息同时到达服务端，服务端也只能一个个处理，所以 Messenger 不适合用于处理大量的并发请求，此时就还是需要考虑使用 AIDL 了，因为 AIDL 支持并发通信

