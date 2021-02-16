[TOC]

## 显示(Explicit) Intent
* 明确要打开的`Activity`，实际都是设置`ComponentName`对象
* `Intent(this, Activity.class)`
* `intent.setClass(this, Activity.class)`
* 以上都等价于`ComponentName("packageName", "activityClassName")`
* 注意：需要在清单文件给`Activity`配置`exported="true"`

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