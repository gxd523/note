[TOC]

## 数据获取
### ModelLoaderRegistry
* `Entry`用来持有Model类型、Data类型，以及对应的`ModelLoaderFactory`
* `add()`向`ModelLoaderRegistry`的entryList集合添加`Entry`
* `build()`：通过`Model`、`Data`匹配出`Entry`，再通过`Entry`的`ModelLoaderFactory.build()`出`ModelLoader`

### ModelLoaderFactory
* 每个`ModelLoader`的实现类都有对应的`ModelLoaderFactory`生成实例

### ModelLoader<Model, Data>
* 通过`buildData(Model model)`获取`LoadData`实例

### LoadData
* 持有`Key`、`DataFetcher`

### DataFetcher
* 有`HttpUrlFetcher`、`FileFetcher`、`AssetPathFetcher`等
* `loadData()`负责加载数据，通过回调返回数据

![](https://gitee.com/hysbtr/pic/raw/master/glide_architecture.png)

## with() 方法的执行过程
* 

## RequestManager
* 管理、启动图片请求
* 当`Context`是`Application`时，`RequestManager`全局单例
* 当`Context`是`Activity`时，每个页面添加一个空`Fragment`，空`Fragment`持有`RequestManager`实例

### RequestTracker
### TargetTracker

### SupportRequestManagerFragment

## RequestBuilder
* 一组静态方法用来创建一个新的RequestManager或者从已经存在的activity和fragment中获取
* 继承`BaseRequestOptions`
* 

## Request

## Target
* 展示图片的载体，这里他封装了ImageView

## Engine
* 负责启动负载以及管理活动和缓存的资源
* 

## DecodeJob

## HttpUrlFetcher

## 缓存池
### LruBitmapPool
* Bitmap的缓存池
* 继承BitmapPool

### LruResourceCache
* Resource缓存池
* 继承MemoryCache

### LruArrayPool
* 存储大小可变的数组的缓存池
* 继承ArrayPool

## 特点
* 对生命周期做相应处理，pause暂停加载，resume恢复加载，destroy取消加载
* 字节数组、Bitmap重用，减少内存开销及回收
* 根据控件大小采样，避免图片分辨率大于控件大小
* 将同一张图片缓存为多种大小，加快显示速度
* 支持动图、webP、mp4
* 灵活更换网络框架


## LRU
* LruCache是由双向链表LinkedHashMap实现的

## 链接
https://www.jianshu.com/p/be6d053e2b6b