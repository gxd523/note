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
3. open .bash_profile(touch .bash_profile)
4. 粘贴一下:

```
JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_65.jdk/Contents/Home
CLASSPAHT=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
PATH=$JAVA_HOME/bin:$PATH:
export JAVA_HOME
export CLASSPATH
export PATH
```
5. wq(保存退出)
6. source .bash_profile
7. java -version

