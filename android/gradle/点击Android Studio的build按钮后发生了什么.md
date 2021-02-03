[TOC]

#### 1、准备依赖包(Preparation of dependecies)
> 在这个阶段gradle检测`application module`依赖的所有`library module`是否ready。这些被依赖的`library module`也要被编译。

#### 2、合并资源并处理清单(Merging resources and processing Manifest)
> 在这个阶段之后，资源和Manifest文件被打包。

#### 3、编译(Compiling)
> 在这个阶段处理编译器的注解，源码被编译成字节码。

#### 4、后期处理(Postprocessing)
> 所有带 “transform”前缀的task都是这个阶段进行处理的。

#### 5、包装和出版(Packaging and publishing)
> 这个阶段library生成.aar文件，application生成.apk文件。

#### 从编译到生成APK的详细步骤
> 图中的矩形表示用到或者生成的文件，椭圆表示工具。

![](https://raw.githubusercontent.com/gxd523/PictureBed/master/gradle_build_process.png)

1. 通过aapt打包res资源文件，生成R.java、resources.arsc和res文件
2. 处理.aidl文件，生成对应的Java接口文件
3. 通过Java Compiler编译R.java、Java接口文件、Java源文件，生成.class文件
4. Proguard混淆.class文件
4. 通过dex命令，将.class文件和第三方库中的.class文件处理生成classes.dex
5. 通过apkbuilder工具，将aapt生成的resources.arsc和res文件、assets文件和classes.dex一起打包生成apk
6. 通过Jarsigner工具，对上面的apk进行debug或release签名
7. 通过zipalign工具，将签名后的apk进行对齐处理。 这样就得到了一个可以安装运行的Android程序。 

