[TOC]
## 下载Ijk代码
- [git地址](https://github.com/Bilibili/ijkplayer.git)

## 安装NDK
1. [NDK下载地址](https://developer.android.com/ndk/downloads/index.html)
2. 配置NDK环境变量
    - 创建配置文件

    ```
    vim ~/.bash_profile
    ```
    - 按i，进入编辑模式

    ```
    export PATH=$PATH:/Users/guoxiaodong/Library/Android/sdk/ndk-bundle
    export ANDROID_NDK=/Users/guoxiaodong/Library/Android/sdk/ndk-bundle
    ```
    - esc，退出编辑模式,:wq保存

## 编译so
1. 修改配置(可选)

	* `module-default.sh`:more codec/format
	* `module-lite-hevc.sh`:less codec/format for smaller binary size(include hevc function)
	* `module-lite.sh`:less codec/format for smaller binary size(by default)

> 如果要选择module-default.sh的话，可能会编译失败，可以参考这篇issue
仔细阅读module-lite.h 原来在最下面有一行配置：
export COMMON_FF_CFG_FLAGS="$COMMON_FF_CFG_FLAGS --disable-linux-perf"
把这行复制到module-default.sh 的最后面就可以了

```
cd config
rm module.sh
ln -s module-default.sh module.sh
```
2. 初始化Android、初始化openssl支持Https(可选)
```
cd ijkplayer/
./init-android.sh
./init-android-openssl.sh
```
3. 编译ffmpeg、openssl
```
cd android/contrib
./compile-openssl.sh clean
./compile-ffmpeg.sh clean
./compile-openssl.sh all
./compile-ffmpeg.sh all
```
4. 编译so
```
cd ..
./compile-ijk.sh all
```
5. so生成目录
```
ijkplayer/android/ijkplayer/中的各个cpu架构的libs目录中生成so文件
```

