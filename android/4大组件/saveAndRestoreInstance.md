[TOC]

## 需要数据恢复的情景
* 资源相关的配置发生改变导致`Activity`被杀死并重新创建，`android:configChanges`
* 资源内存不足导致低优先级的 Activity 被杀死，或开发者模式开启`不保留活动`

## 3种数据恢复方式
### 使用onSaveInstanceState()、onRestoreInstanceState()
* 序列化存储到磁盘
* 适用小对象存储，否则I/O会很慢

### 使用使用onRetainNonConfigurationInstance()、getLastNonConfigurationInstance()
* 存储到内存中
* 支持复杂对象，速度也很快
* 不保存本地

### 使用Fragment的setRetainInstance()
* Android3.0之后推出
* 当配置发生改变时，`Fragment`会随着宿主`Activity`销毁与重建
* 当我们调用`Fragment.setRetainInstance(true)`方法时，系统允许 `Fragment`绕开`销毁-重建`的过程，在`Activity`重建时，保留`Fragment`的实例
* 存储到内存中
* 支持复杂对象，速度也很快
* 不保存本地

