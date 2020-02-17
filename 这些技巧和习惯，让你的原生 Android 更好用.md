### 卸载应用

1. 连接adb，输入命令`adb shell`
2. 输入命令`pm list packages | grep 'com.baidu.input_yijia'`
3. 输入命令`pm uninstall -k --user 0 'com.baidu.input_yijia'`
大功告成！不用root即可删除系统预装软件！

1. 输入命令`brew install android-platform-tools`，安装adb
2. 输入命令`adb reboot recovery/bootloader`，重启进入刷机模式
3. 

