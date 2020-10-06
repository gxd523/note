[TOC]
### Android系统架构图
![](https://github.com/gxd523/note/raw/master/pic/android_system_structure.png)

### Android系统启动流程图
![](https://github.com/gxd523/note/raw/master/pic/android_system_launch_process.png)

* init进程是Native世界的第一个进程

### Zygote
* Zygote是init进程通过解析init.rc文件创建出的进程
* Zygote是Java世界的第一个进程
* SystemServer是Zygote创建的第一个进程
* 之后Zygote就用来fork出应用进程
* Zygote进程与其他进程间通过Socket进行通信

### SystemServer
> SystemServer进程是系统进程，很多系统服务，例如ActivityManagerService、PackageManagerService、WindowManagerService…都是存在该进程被创建后启动

