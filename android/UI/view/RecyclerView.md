类名 | 作用
--- | ---
RecyclerView.LayoutManager | 负责Item视图的布局的显示管理
RecyclerView.ItemDecoration | 给每一项Item视图添加子View,例如可以进行画分隔线之类
RecyclerView.ItemAnimator | 负责处理数据添加或者删除时候的动画效果
RecyclerView.Adapter | 为每一项Item创建视图
RecyclerView.ViewHolder | 承载Item视图的子布局
RecyclerView.Recycler | 负责处理View的缓存

https://blog.csdn.net/weixin_43130724/article/details/90068112

## 缓存
### Recycler.mAttachedScrap
屏幕内的ViewHolder

### Recycler.mCachedViews
刚移出屏幕的ViewHolder，`mViewCacheMax`默认2个，向上滑动后`mViewCacheMax`改为3个，上边2个，下边1个，向下滑动反过来，根据position获取，复用时不用走`onBindViewHolder`

### Recycler.mViewCacheExtension
留给开发者自定义缓存

### Recycler.mRecyclerPool.mScrap.mScrapHeap
Cache满了后，根据FIFO，移入`RecycledViewPool`，默认5个，ViewHolder数据会被重置，根据`itemType`获取，复用时要走`onBindViewHolder`，对应源码`Recycler.mRecyclerPool`

## Recyclerview使用优化
* `onBindViewHolder`在即将滑出但还未滑出时就会被调用
* `onBindViewHolder`在`UI线程`执行，耗时太多会影响流畅性
* 降低Item的布局复杂度，只对`onCreateViewHolder`有点用
* 不要在`onBindViewHolder`中创建实例，或IO等操作
* 懒加载，在滑动过程中不执行`onBindViewHolder`中的代码 
* 共用缓存View：`RecyclerView.setRecycledViewPool(pool)`，如果LayoutManager是LinearLayoutManager或其子类，需要手动开启这个特性：layout.setRecycleChildrenOnDetach(true)
* 加大RecyclerView的缓存，空间换时间
	* `setItemViewCacheSize()`：设置cache缓存个数
	* `setDrawingCacheEnabled()`：将View显示内容缓存为Bitmap
* 使用`RecyclerView.setHasFixedSize(true)`减少`RecyclerView.requestLayout()`：
	* `onItemRangeChanged()`
	* `onItemRangeInserted()`
	* `onItemRangeRemoved()`
	* `onItemRangeMoved()`