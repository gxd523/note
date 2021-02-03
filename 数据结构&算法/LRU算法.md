![](https://raw.githubusercontent.com/gxd523/PictureBed/master/lru.png)

* sizeOf()：覆写此方法实现自己的一套定义计算entry大小的规则。
* V create(K key)：如果key对象缓存被移除了，则调用次方法重建缓存
* entryRemoved(boolean evicted, K key, V oldValue, V newValue) ：当key对应的缓存被删除时回调该方法。

https://juejin.im/post/6844903556705681421#heading-0