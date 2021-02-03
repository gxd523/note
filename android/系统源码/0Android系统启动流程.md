[TOC]
### Android系统架构图
![](https://raw.githubusercontent.com/gxd523/PictureBed/master/android_system_structure.png)

### Android系统启动流程图
![](https://raw.githubusercontent.com/gxd523/PictureBed/master/android_system_launch_process.png)

* init进程是Native世界的第一个进程

### Zygote
* Zygote是init进程通过解析init.rc文件创建出的进程
* Zygote是Java世界的第一个进程
* SystemServer是Zygote创建的第一个进程
* 之后Zygote就用来fork出应用进程
* Zygote进程与其他进程间通过Socket进行通信

### SystemServer
> SystemServer进程是系统进程，很多系统服务，例如ActivityManagerService、PackageManagerService、WindowManagerService…都是存在该进程被创建后启动

### ServiceManager
> Binder基于C/S架构的通讯模型，其中定义了4个角色：Client、Server、Binder驱动和ServiceManager。

* Binder驱动类似路由器，负责将Client的请求转发到具体的Server中执行，并将Server返回的数据传回给Client。
* 类似网络通信中的DNS服务器，负责将Client请求的Binder描述符转化为具体的Server地址，以便Binder驱动能够转发给具体的Server。Server如需提供Binder服务，需要向ServiceManager注册。