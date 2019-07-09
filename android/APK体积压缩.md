* 图片转成WebP格式，WebP 比 PNG 小了45%
* 去除不必要的so文件
```
ndk {
    abiFilters "armeabi"
}
```

* 使用Link去除无用资源`Analyze`>`Run Inspection By Name`>`Unused Resources`(注意：Link只能识别R.xx.xx方式使用的资源，如果使用getIdentifier()方式加载的资源将被忽略)

* 开启混淆

* AndResGuard 微信资源压缩方案

