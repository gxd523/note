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

