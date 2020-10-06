[TOC]

[原文地址](https://mp.weixin.qq.com/s/8ayLeGavaYfMxqLoTxFIlQ)

> jadx 一个非常好用的 Android 反编译的工具,源代码已经在 Github 上开源

## 优点
* 图形化的界面
* 拖拽式的操作
* 反编译输出 Java 代码
* 导出 Gradle 工程

## 下载
[jadx已在 GitHub 上开源可以直接下载](https://github.com/skylot/jadx/releases)

## 安装&使用
下载好后解压，得到以下目录，mac下点击`jadx-gui`文件运行
![](https://github.com/gxd523/note/raw/master/pic/jadx_pic_a.png)

## 功能
#### 强大的搜索功能
jadx 提供的搜索功能，非常强大，而且搜索速度也不慢。
你可以点击 Navigation -> Text Search 或者 Navigation -> Class Search 激活它。
![](https://github.com/gxd523/note/raw/master/pic/jadx_pic_b.png)
![](https://github.com/gxd523/note/raw/master/pic/jadx_pic_c.png)
jadx 的搜索，支持四种维度，**Class**、**Method**、**Field**、**Code**，我们可以根据我们搜索的内容进行勾选，范围最大的就是 Code ，基本上就是文本匹配搜索。这里反编译的 Apk 集成了支付宝支付，所以能搜到 alipay 的内容。

#### 直接搜索到引用的代码
![](https://github.com/gxd523/note/raw/master/pic/jadx_pic_d.png)

#### deobfuscation
一般 Apk 在发布出去之前，都是会被混淆的，这基本上国内 App 的标配。这样一个类，最终会被混淆成 a.b.c ，方法也会变成 a.b.c.a() ，这样其实非常不利于我们阅读。我们很难看到一个 a.java 的文件，就确定它是哪一个，还需要根据包名来区分。
而 deobfusation 功能，可以为它们其一个特殊的名字，这样它在这个项目中，名字就唯一了，方便我们识别和搜索。
这个功能可以在 Tools -> deobfusation 中激活。
![](https://github.com/gxd523/note/raw/master/pic/jadx_pic_e.png)

#### 一键导出 Gradle 工程
主要是为了借助 AS 强大的 IDE 功能，例如方法跳转、引用搜索等等，让我们阅读起来更方便。
![](https://github.com/gxd523/note/raw/master/pic/jadx_pic_f.png)

#### jadx 的错误处理
有时候有代码，反编译的不完整，你会看到 JADX WARNING : inconsistent code 标志的错误。
![](https://github.com/gxd523/note/raw/master/pic/jadx_pic_g.png)

