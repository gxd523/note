BusyBox 是一个集成了三百多个最常用Linux命令和工具的软件。BusyBox 包含了一些简单的工具，例如ls、cat和echo等等，还包含了一些更大、更复杂的工具，例grep、find、mount以及telnet。它集成压缩了 Linux 的许多工具和命令，也包含了 Android 系统的自带的shell。

### Android设备安装busybox
* root设备
* 选择armv7l下载：http://www.busybox.net/downloads/binaries
* 连接adb，将下载文件推送到Android设备，`adb push ~/Desktop/busybox /mnt/sdcard`
* 进入shell，su，输入以下命令，为了在/system目录写入文件：`mount -o remount,rw -t yaffs2 /dev/block/mtdblock3 /system`
* 复制 busybox 文件到 /system/xbin：`cp /mnt/sdcard/busybox /system/xbin`
* 给busybox分配可执行权限：`chmod 755 busybox`
* 

