[TOC]
## 常用命令
- 查看.文件

```
ls -la
```
- 根目录从用户名开始

```
defaults delete com.apple.finder PathBarRootAtHome;killall Finder
```
- Finder显示文件路径

```
defaults write com.apple.finder _FXShowPosixPathInTitle -bool YES
```
- 始终显示资料库

```
chflags nohidden ~/Library/
```
- 显示隐藏文件

```
defaults write com.apple.finder AppleShowAllFiles -boolean true ; killall Finder
```
- Terminal打开Finder

```
open Library/Android/sdk/
```
## 配置jdk
1. cd ~
2. sudo su
3. open .bash_profile(touch .bash_profile)，新的是`.zprofile`
4. 粘贴一下:

```
export JAVA_HOME=/Applications/Android\ Studio.app/Contents/jre/Contents/Home

export ANDROID_SDK_HOME=/Users/guoxiaodong/Library/Android/sdk

export PATH=$PATH:$ANDROID_SDK_HOME/platform-tools
```
5. wq(保存退出)
6. source .bash_profile
7. java -version

### option(⌥)的隐藏彩蛋
* `⌘+⌥+ESC`：强制退出框
* 文件右键弹出菜单时按住⌥键：拷贝文件绝对路径、始终以此方式打开
* `⌥+Space`：全屏预览
* 多选文件，⌘+⌥+i：多个文件大小

### MD5、SHA1、SHA256
* sha1：`shasum -a 1 a.txt`
* sha256：`shasum -a 256 a.txt`

### 查看连接设备传输速率
* 命令行输入：`system_profiler SPUSBDataType`