[TOC]

## ADB(Android Debug Bridge)
> adb就是连接Android手机与PC端的桥梁，可以让用户在电脑上对手机进行全面的操作。

* 查看前台Activity：`adb shell dumpsys activity activities | grep mFocusedActivity`
* 启动adb server：`adb -P <端口> start-server`
* 停止adb server：`adb kill-server`
* 重启adb server：`adb restart-server`
* adb版本：`adb version`
* Root：`adb root`
* 卸载：`adb shell pm uninstall --user 0 <包名>`
* 获取包列表：`adb shell pm list package`
* 获取包名对应apk所在路径：`adb shell pm list packages -f | grep com.xiaomi`
* 导出文件：`adb pull <已安装apk路径> <导出路径>`
* 启动Activity：`adb shell am start -n <包名>/<Activity全名>`
* 导入文件：`adb push <电脑上的文件路径> <设备里的目录>`
* 查看设备型号：`adb shell getprop ro.product.<brand、model、name>`
* 查看设备屏幕分辨率：`adb shell wm size`
* 查看设备屏幕像素密度：`adb shell wm density`
* 查看AndroidID：`adb shell settings get secure android_id`
* 查看Android版本：`adb shell getprop ro.build.version.<sdk、release>`
* 查看MAC地址：`adb shell cat /sys/class/net/wlan0/address`
* 查看CPU信息：`adb shell cat /proc/cpuinfo`
* 截图：`adb shell screencap -p /sdcard/sc.png`
* 录屏：`adb shell screenrecord /sdcard/filename.mp4`，ctrl+c结束
* 查看wifi密码(需root)：`adb shell`、`su`、`cat /data/misc/wifi`
* 重启Recovery：`adb reboot recovery`
* 重启到Fastboot：`adb reboot bootloader`
* 查看像素密度：`adb shell getprop ro.sf.lcd_density`
* 查看实时资源占用情况：`adb shell top`
* 查看任务栈命令：`adb shell dumpsys activity | grep 'Running activities' -A 5`

## 进程
* 杀进程：`adb shell kill [PID]`
* 查看进程：`adb shell ps | grep <packageName>`
USER | PID | PPID(父进程ID) | VSIZE(虚拟内存) | RSS(实际内存) | WCHAN | PC | NAME
:---: | :---: | :---: | :---: | :---: | :---: | :---: | :---:
u0_a189 | 16518 | 383 | 1247192 | 36912 | ffffffff | 00000000 | com.netease.urs.eee 
* 查看进程状态：`adb shell ps -x [PID]`
* 实时监听进程变化：`adb shell top|grep <package_name>`

列名 | 含义
--- | ---
PID | 进程ID
PR | 优先级
CPU% | 当前瞬间占用CPU百分比
S | 进程状态(R=运行，S=睡眠，T=跟踪/停止，Z=僵尸进程)
\#THR | 线程数
VSS | Virtual Set Size虚拟耗用内存(包含共享库占用的内存)
RSS | Resident Set Size实际使用物理内存(包含共享库占用的内存)
PCY | 调度策略优先级, SP_ BACKGROUND/SPFOREGROUND
UID | 进程所有者的用户ID
NAME | 进程名

## 模拟输入
* 模拟按键输入：`adb shell input keyevent <keycode>`
keycode | 含义
:--- | :---:
KEYCODE_DPAD_UP | 19
KEYCODE_DPAD_DOWN | 20
KEYCODE_DPAD_LEFT | 21
KEYCODE_DPAD_RIGHT | 22
KEYCODE_DPAD_CENTER | 23
KEYCODE_BACK | 4
KEYCODE_ENTER | 66

## 模拟点击
* 模拟点击：`adb shell input tap <x坐标> <y坐标>`

### dumpsys
> 获得连接设备的所有系统服务的诊断输出

* 查看前台Activity：`adb shell dumpsys activity activities | grep mFocusedActivity`
* 查看任务栈：`adb -s 2fb0054d shell dumpsys activity activities | grep Activities | grep <包名>`
* 查看可与 dumpsys 配合使用的系统服务的完整列表：`adb shell dumpsys -l`
* 查看系统网络情况：`adb shell dumpsys netstats detail`
* 查看内存：`adb shell dumpsys meminfo <包名>`
* 查看设备电池状况：`adb shell dumpsys battery`
* dumpsys：`adb shell dumpsys <activity|package|cpuinfo|meminfo|battery|window> -p <包名>`
* 查看任务栈：`adb shell dumpsys activity activities | sed -En -e '/Running activities/,/Run #0/p'`
* 查看apk信息：`adb shell dumpsys package <包名>`
* 帮助：`adb shell dumpsys package -h`

### 安装&卸载
* `-r`：覆盖安装、保留数据和缓存文件
* `-s`：指定安装位置
* `-k`：保留配置和缓存文件
* `-d`：允许安装debug

### adb常见错误
输出 | 含义 | 解决办法
:--- | :--- | :---
INSTALL_FAILED_ALREADY_EXISTS | 应用已经存在 | 使用 -r 参数
INSTALL_FAILED_INVALID_APK | 无效的 APK 文件|
INSTALL_FAILED_INVALID_URI | 无效的 APK 文件名 | 确保 APK 文件名里无中文
INSTALL_FAILED_INSUFFICIENT_STORAGE | 空间不足 | 清理空间
INSTALL_FAILED_DUPLICATE_PACKAGE | 已经存在同名程序
INSTALL_FAILED_NO_SHARED_USER | 请求的共享用户不存在
INSTALL_FAILED_UPDATE_INCOMPATIBLE | 已经安装过签名不一样的同名应用，且数据没有移除
INSTALL_FAILED_SHARED_USER_INCOMPATIBLE | 请求的共享用户存在但签名不一致
INSTALL_FAILED_MISSING_SHARED_LIBRARY | 安装包使用了设备上不可用的共享库
INSTALL_FAILED_REPLACE_COULDNT_DELETE | 替换时无法删除
INSTALL_FAILED_DEXOPT | dex 优化验证失败或空间不足
INSTALL_FAILED_OLDER_SDK | 设备系统版本低于应用要求
INSTALL_FAILED_CONFLICTING_PROVIDER | 设备里已经存在与应用里同名的 content provider
INSTALL_FAILED_NEWER_SDK | 设备系统版本高于应用要求
INSTALL_FAILED_TEST_ONLY | 应用是 test-only 的，但安装时没有指定 -t 参数
INSTALL_FAILED_CPU_ABI_INCOMPATIBLE | 包含不兼容设备 CPU 应用程序二进制接口的 native code
INSTALL_FAILED_MISSING_FEATURE | 应用使用了设备不可用的功能
INSTALL_FAILED_CONTAINER_ERROR | sdcard 访问失败 | 确认 sdcard 可用，或者安装到内置存储
INSTALL_FAILED_INVALID_INSTALL_LOCATION | 不能安装到指定位置 | 切换安装位置，添加或删除 -s 参数
INSTALL_FAILED_MEDIA_UNAVAILABLE | 安装位置不可用 | 一般为 sdcard，确认 sdcard 可用或安装到内置存储
INSTALL_FAILED_VERIFICATION_TIMEOUT | 验证安装包超时
INSTALL_FAILED_VERIFICATION_FAILURE | 验证安装包失败
INSTALL_FAILED_PACKAGE_CHANGED | 应用与调用程序期望的不一致
INSTALL_FAILED_UID_CHANGED | 以前安装过该应用，与本次分配的 UID 不一致 | 清除以前安装过的残留文件
INSTALL_FAILED_VERSION_DOWNGRADE | 已经安装了该应用更高版本 | 使用 -d 参数
INSTALL_FAILED_PERMISSION_MODEL_DOWNGRADE | 已安装 target SDK 支持运行时权限的同名应用，要安装的版本不支持运行时权限
INSTALL_PARSE_FAILED_NOT_APK | 指定路径不是文件，或不是以 .apk结尾
INSTALL_PARSE_FAILED_BAD_MANIFEST | 无法解析的 AndroidManifest.xml 文件
INSTALL_PARSE_FAILED_UNEXPECTED_EXCEPTION | 解析器遇到异常
INSTALL_PARSE_FAILED_NO_CERTIFICATES | 安装包没有签名
INSTALL_PARSE_FAILED_INCONSISTENT_CERTIFICATES | 已安装该应用，且签名与 APK 文件不一致 | 先卸载设备上的该应用，再安装
INSTALL_PARSE_FAILED_CERTIFICATE_ENCODING | 解析 APK 文件时遇到CertificateEncodingException
INSTALL_PARSE_FAILED_BAD_PACKAGE_NAME | manifest 文件里没有或者使用了无效的包名
INSTALL_PARSE_FAILED_BAD_SHARED_USER_ID | manifest 文件里指定了无效的共享用户 ID
INSTALL_PARSE_FAILED_MANIFEST_MALFORMED | 解析 manifest 文件时遇到结构性错误
INSTALL_PARSE_FAILED_MANIFEST_EMPTY | 在 manifest 文件里找不到找可操作标签（instrumentation 或 application
INSTALL_FAILED_INTERNAL_ERROR | 因系统问题安装失败
INSTALL_FAILED_USER_RESTRICTED | 用户被限制安装应用
INSTALL_FAILED_DUPLICATE_PERMISSION | 应用尝试定义一个已经存在的权限名称
INSTALL_FAILED_NO_MATCHING_ABIS | 应用包含设备的应用程序二进制接口不支持的 native code
INSTALL_CANCELED_BY_USER | 应用安装需要在设备上确认，但未操作设备或点了取消 | 在设备上同意安装
INSTALL_FAILED_ACWF_INCOMPATIBLE | 应用程序与设备不兼容
does not contain AndroidManifest.xml | 无效的 APK 文件
is not a valid zip file | 无效的 APK 文件
Offline | 设备未连接成功 | 先将设备与 adb 连接成功
unauthorized | 设备未授权允许调试
error: device not found | 没有连接成功的设备 | 先将设备与 adb 连接成功
protocol failure | 设备已断开连接 | 先将设备与 adb 连接成功
Unknown option: -s | Android 2.2 以下不支持安装到 sdcard | 不使用 -s 参数
No space left on devicerm | 空间不足 | 清理空间
Permission denied ... sdcard ... | sdcard 不可用

## 实用
* 截图保存桌面：`adb shell screencap -p /sdcard/sc.png && adb pull /sdcard/sc.png Desktop/a.png`
* 重启应用：`adb shell am force-stop <包名> && adb shell am start -n <包名>/<页面名>`

## adb连接失败
* `unauthorized`：第一次连接设备需要在设备上授权确认

## 无线调试
* 电脑手机连接同一个wifi
* 手机电脑先用数据线连接adb，执行`adb tcpip 5555`
* `adb connect <ip>:5555`，连接成功即可拔掉数据线
* 如果提示：`unable to connect to <ip>:5555: Connection refused`
* 则执行`adb tcpip 5555`，再次：`adb connect <ip>:5555`

