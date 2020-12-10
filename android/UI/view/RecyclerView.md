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
* 滚动过程中，在下一个ViewHolder还未显示时就要从`RecyclerPool`或者创建一个放入`Cache`中，当显示下一个ViewHolder时，再从`Cache`中拿到屏幕显示
* 当屏幕已显示最多能显示的ViewHolder时，当滚动显示下一个ViewHolder时，会优先从`RecyclerPool`中拿一个ViewHolder放入`Cache`缓存中，`RecyclerPool`没有，则创建一个ViewHolder，并执行`onBindViewHolder()`
* `onBindViewHolder`在`UI线程`执行，耗时太多会影响流畅性
1. 降低Item的布局复杂度，只对`onCreateViewHolder`有点用
2. 不要在`onBindViewHolder()`中创建实例，避免频繁GC，占用绘制时间
3. 不要在`onBindViewHolder()`中执行耗时操作，占用绘制时间
4. 懒加载，在滑动过程中不执行`onBindViewHolder`中的代码，避免2、3
5. 共用缓存View：`RecyclerView.setRecycledViewPool(pool)`，如果LayoutManager是LinearLayoutManager或其子类，需要手动开启这个特性：layout.setRecycleChildrenOnDetach(true)
6. 加大RecyclerView的缓存，空间换时间
	* `setItemViewCacheSize()`：设置cache缓存个数
	* `setDrawingCacheEnabled()`：将View显示内容缓存为Bitmap
7. 如果Item高度固定，使用`RecyclerView.setHasFixedSize(true)`减少`RecyclerView.requestLayout()`：
	* `onItemRangeChanged()`
	* `onItemRangeInserted()`
	* `onItemRangeRemoved()`
	* `onItemRangeMoved()`