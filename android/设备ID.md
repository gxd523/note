[TOC]

## Android ID
* 当设备在第一次启动时，系统会随机产生一个64位的数字，然后以16进制的形式保存在设备上
* `Settings.System.getString(contentResolver, Settings.Secure.ANDROID_ID)`
* 刷机、恢复出厂设置后该值会被重置
* 不同设备可能产生相同值
* 有的厂商设备可能获取为null

## 硬件序列码
* `Build.SERIAL`

## 指纹
* `Build.FINGERPRINT`

## IMEI
* International Mobile Equipment Identity，国际移动设备身份码的缩写
* 是由15位数字组成的“电子串号”，它与每台手机一一对应，每个IMEI在世界上都是唯一的

## IDFA
* Identifier For Advertising，iOS独有的广告标识符

## UDID
* Unique Device Identifier，唯一设备标识码

## UUID
* Universally Unique Identifier，通用唯一识别码
* 目前最广泛应用的UUID，是微软公司的全局唯一标识符GUID
* 其目的是让分布式系统中的所有元素，都能有唯一的辨识信息，而不需要通过中央控制端来做辨识信息的指定。