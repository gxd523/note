## 特点
* 对生命周期做相应处理，pause暂停加载，resume恢复加载，destroy取消加载
* 字节数组、Bitmap重用，减少内存开销及回收
* 根据控件大小采样，避免图片分辨率大于控件大小
* 将同一张图片缓存为多种大小，加快显示速度
* 支持动图、webP、mp4
* 灵活更换网络框架

## 四级缓存
### Active Resources(活动缓存)
* 缓存当前正在使用图片资源
* 使用value为弱引用的HashMap缓存
* 通过图片计数器判断是否正在使用，引用数为0，则放入内存缓存

### Memory Cache(内存缓存)
* 缓存之前使用过的图片资源

### Resource Disk Cache(处理后的磁盘缓存)
* 解码后存入磁盘的图片资源文件，解码包括图片参数inSampleSize、inPreferredConfig

### Data Disk Cache(原数据磁盘缓存)
* 存入磁盘的原始图片资源文件，从网络、文件中直接获得的原始数据
* 通过`diskCacheStrategy()`设置：
策略 | 含义
:---: | :---:
DiskCacheStrategy.NONE | 表示不缓存任何内容
DiskCacheStrategy.SOURCE | 表示只缓存原始图片
DiskCacheStrategy.RESULT | 表示只缓存转换过后的图片（默认选项）
DiskCacheStrategy.ALL  | 表示既缓存原始图片，也缓存转换过后的图片

## LRU
* LruCache是由双向链表LinkedHashMap实现的

## 链接
https://www.jianshu.com/p/be6d053e2b6b