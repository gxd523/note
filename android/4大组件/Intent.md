[TOC]

## Action
### 跳转系统界面
Action | 相关界面
:---: | ---
系统设置界面 | ACTION_SETTINGS
APN设置界面 | ACTION_APN_SETTINGS
定位设置界面 | ACTION_LOCATION_SOURCE_SETTINGS
更多连接方式设置界面 | ACTION_AIRPLANE_MODE_SETTINGS
双卡和移动网络设置界面 | ACTION_DATA_ROAMING_SETTINGS
无障碍设置界面 | ACTION_ACCESSIBILITY_SETTINGS
同步设置界面 | ACTION_SYNC_SETTINGS
添加账户界面 | ACTION_ADD_ACCOUNT
选取运营商的界面 | ACTION_NETWORK_OPERATOR_SETTINGS
安全设置界面 | ACTION_SECURITY_SETTINGS
备份重置设置界面 | ACTION_PRIVACY_SETTINGS
VPN设置界面,可能不存在 | ACTION_VPN_SETTINGS
无线网设置界面 | ACTION_WIFI_SETTINGS
WIFI的IP设置 | ACTION_WIFI_IP_SETTINGS
蓝牙设置 | ACTION_BLUETOOTH_SETTINGS
投射设置 | ACTION_CAST_SETTINGS
日期时间设置 | ACTION_DATE_SETTINGS
声音设置 | ACTION_SOUND_SETTINGS
显示设置 | ACTION_DISPLAY_SETTINGS
语言设置 | ACTION_LOCALE_SETTINGS
辅助应用和语音输入设置 | ACTION_VOICE_INPUT_SETTINGS
语言和输入法设置 | ACTION_INPUT_METHOD_SETTINGS
个人字典设置界面 | ACTION_USER_DICTIONARY_SETTINGS
存储空间设置的界面 | ACTION_INTERNAL_STORAGE_SETTINGS
搜索设置界面 | ACTION_SEARCH_SETTINGS
开发者选项设置 | ACTION_APPLICATION_DEVELOPMENT_SETTINGS
手机状态信息的界面 | ACTION_DEVICE_INFO_SETTINGS
互动屏保设置的界面 | ACTION_DREAM_SETTINGS
通知使用权设置的界面 | ACTION_NOTIFICATION_LISTENER_SETTINGS
勿扰权限设置的界面 | ACTION_NOTIFICATION_POLICY_ACCESS_SETTINGS
字幕设置的界面 | ACTION_CAPTIONING_SETTINGS
打印设置界面 | ACTION_PRINT_SETTINGS
节电助手界面 | ACTION_BATTERY_SAVER_SETTINGS
主屏幕设置界面 | ACTION_HOME_SETTINGS

### 特殊Action
* `android.intent.action.MAIN`：默认页面，配合`android.intent.category.LAUNCHER`一起用

## Category
### 特殊Category
* `android.intent.category.DEFAULT`：隐式Intent，必须有一个category，默认是这个
* `android.intent.category.LAUNCHER`：启动页面
* `android.intent.category.HOME`：桌面应用

## 显示(Explicit) Intent
* 明确要打开的`Activity`，实际都是设置`ComponentName`对象
* `Intent(this, Activity.class)`
* `intent.setClass(this, Activity.class)`
* 以上都等价于`ComponentName("packageName", "activityClassName")`
* 注意：跨应用启动，需要在清单文件给`Activity`配置`exported="true"`

## 隐式(Implicit) Intent
* 不明确指定要打开的`Activity`，通过`Action`、`Category`、`Data`筛选出匹配的`Activity`
* 通过`Action`、`Category`，注意`Category`一定要设置
```xml
<intent-filter>
    <action android:name="abc" />
    <category android:name="android.intent.category.DEFAULT" />
</intent-filter>
```
* 通过`Data`方式，注意`Action`、`Category`也一定要指定，虽然跳转时不用设置：
```xml
<intent-filter>
    <action android:name="abc" />
    <category android:name="android.intent.category.DEFAULT" />
    <data
        android:host="www.baidu.com"
        android:path="/picture"
        android:scheme="http" />
</intent-filter>
```