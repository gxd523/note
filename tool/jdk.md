[TOC]

### JDK卸载
1、sudo rm -rf /Library/Internet\ Plug-Ins/JavaAppletPlugin.plugin
2、sudo rm -rf /Library/PreferencePanes/JavaControlPanel.prefPane
3、sudo rm -rf ~/Library/Application\ Support/Oracle/Java
4、sudo rm -rf /Library/Java/JavaVirtualMachines/jdk1.8.0_221.jdk

### JDK环境配置
1. `vim ~/.bash_profile`
2. 按`i`，进入编辑模式(`:`是分隔符)
```
export PATH=$PATH:/Users/guoxiaodong/Library/Android/sdk/ndk-bundle
export ANDROID_NDK=/Users/guoxiaodong/Library/Android/sdk/ndk-bundle

ANDROID_SDK_HOME=/Users/guoxiaodong/Library/Android/sdk
export PATH=$PATH:$ANDROID_SDK_HOME/tools
export PATH=$PATH:$ANDROID_SDK_HOME/platform-tools

JAVA_HOME=/Applications/Android\ Studio.app/Contents/jre/jdk/Contents/Home
CLASSPAHT=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
PATH=$JAVA_HOME/bin:$PATH:
export JAVA_HOME
export CLASSPATH
export PATH
```
3. 按`Esc`，退出编辑模式，`:wq`保存

### Java各种概念
* 我们平时用的都是java se，包括服务端
* JDK(Java Development Kit)：开发Java软件时要安装，JDK包含JRE
* JRE(Java Runtime Environmnet)：java运行环境，能运行java就说明安装了，一般服务器安装
* JVM(Java Virtual Machine)：运行java程序的java虚拟机，JRE包含JVM，JRE除了JVM还包含java类库基础模块等
* Java1.5=Java5