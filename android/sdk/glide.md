## 特点
* 对生命周期做相应处理
* 字节数组、Bitmap重用，减少内存开销及回收
* 根据控件大小采样，避免图片分辨率大于控件大小
* 将同一张图片缓存为多种大小，加快显示速度
* 支持动图
* 灵活更换网络框架

## 四级缓存
* Active Resources：当前正在使用图片资源
* Memory Cache：之前使用过的图片资源
* Resource Disk Cache：解码后存入磁盘的图片资源文件，解码包括图片参数inSampleSize、inPreferredConfig
* Data Disk Cache：存入磁盘的原始图片资源文件，从网络、文件中直接获得的原始数据

## Lru
* LruCache是由双向链表LinkedHashMap实现的
* Bitmap的inMutable需要为true。
* Android 4.4及以上只需要被复用的Bitmap的内存必须大于等于需要新获得Bitmap的内存，则允许复用此Bitmap。getByteCount()小于等于被复用的Bitmap
* 4.4以下(3.0以上)则被复用的Bitmap与使用复用的Bitmap必须宽、高相等并且使用复用的Bitmap解码时设置的inSampleSize为1，才允许复用。getByteCount()等于被复用的Bitmap