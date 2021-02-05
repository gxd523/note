[TOC]

## Binder优点
* 效率高：`Binder`使用内存映射，只需要一次数据拷贝
* 其他IPC，除`内存共享`外，都需要进行两次数据拷贝
* 更安全：接收方可以从数据包中获取发送方的进程id、用户id，方便验证发送方的身份
* 其他IPC想要实现，只能够主动存入，但是这有可能在发送的过程中被修改

## 传统IPC

![](https://raw.githubusercontent.com/gxd523/PictureBed/master/ipc_other.jpg)

* 进程间的`用户空间`是相互隔离的
* `内核空间`与`用户空间`也是相互隔离的
* 传统IPC需要先调用`copy_from_user()`，把数据从`用户空间`拷贝到`内核空间`
* 再调用`copy_to_user()`，把`内核空间`的数据拷贝到接收方的`用户空间`

## Binder的IPC

![](https://raw.githubusercontent.com/gxd523/PictureBed/master/ipc_binder.jpg)

* `Binder`基于`内存映射(mmap)`，减少了一次数据拷贝
* `内存映射(mmap)`是指不同的虚拟内存(`内核空间`、`用户空间`都是指虚拟内存)，指向相同的物理内存
* `Binder`先在内核空间创建一个`数据接收缓存区`
* 接着创建`内核缓存区`，并建立`内核缓存`、`数据接收缓存`、接收进程`用户空间`的内存地址映射关系
* 发送方只需要调用`copy_from_user()`，把数据拷贝到`内核缓存区`，通过映射即可响应到接收方的`用户空间`

## Binder的C/S架构

![](https://raw.githubusercontent.com/gxd523/PictureBed/master/binder_cs.jpg)

* `Binder`框架定义了4个角色：`Server`、`Client`、`ServiceManager`、`Binder驱动`
* `Server`、`Client`、`ServiceManager`运行在`用户空间`，`Binder驱动`运行在`内核空间`

## Binder通信过程

![](https://raw.githubusercontent.com/gxd523/PictureBed/master/binder_abstract.jpg)

1. `Server`在`ServiceManager`中注册：`Server`进程在创建的时候，也会创建对应的`Binder`实体，如果要提供服务给`Client`，就必须为`Binder`实体注册一个名字
2. `Client`通过`ServiceManager`获取服务：`Client`知道服务中`Binder`实体的名字后，通过名字从`ServiceManager`获取`Binder`实体的引用
3. `client`通过获得一个`server`的代理接口，对`server`进行调用
4. 代理接口中定义的方法与`server`中定义的方法时一一对应的
5. `client`调用某个代理接口中的方法时，代理接口的方法会通过`IBinder.transact()`将`client`传递的参数打包成`Parcel`对象
6. 代理接口将序列化的`Parcel`通过`Binder驱动`拷贝到`内核空间`，在映射到服务端的`用户空间`
7. `server`会读取`Binder驱动`中的请求数据，如果是发送给自己的，通过`IBinder.onTransact()`反序列化`Parcel`对象，处理并将结果返回
8. 整个的调用过程是一个同步过程，在`server`处理的时候，`client`会block住。因此`client`调用过程不应在主线程