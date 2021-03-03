[TOC]

类名 | 作用
--- | ---
RecyclerView.LayoutManager | 负责Item视图的布局的显示管理
RecyclerView.ItemDecoration | 给每一项Item视图添加子View,例如可以进行画分隔线之类
RecyclerView.ItemAnimator | 负责处理数据添加或者删除时候的动画效果
RecyclerView.Adapter | 为每一项Item创建视图
RecyclerView.ViewHolder | 承载Item视图的子布局
RecyclerView.Recycler | 负责处理View的缓存

https://blog.csdn.net/weixin_43130724/article/details/90068112


* tryGetViewHolderForPositionByDeadline
* 

## 预取机制
* CPU处理完绘制数据后就处于空闲状态，直到下一帧，预取机制会将接下来可能要显示的Item在这一帧就用CPU处理，然后将数据保存到ViewHolder
* `LinearLayoutManager.setInitialItemPrefetchCount()`：默认2个


## 缓存优先级
* `tryGetViewHolderForPositionByDeadline()`：依次从缓存中寻找`ViewHolder`，都没找到再创建`ViewHolder`

### 复用性能排序
* 最好情况：复用`ViewHolder`，且无需重新`bindViewHolder()`
	* 包括以下缓存：`mChangedScrap`、`mAttachedScrap`、`mCachedViews`
* 次好情况：复用`ViewHolder`，重新`bindViewHolder()``
	* 包括以下缓存：`mRecyclerPool`
* 最坏情况：创建新的`ViewHolder`，并`bindViewHolder()`

### mChangedScrap
### mAttachedScrap
* 用于布局过程中屏幕可见`Item`的回收和复用
* mAttachedScrap生命周期起始于RecyclerView布局开始，终止于RecyclerView布局结束
* 每次向`RecyclerView`填充`Item`之前都会：
	* 先清空屏幕上的`Item`，将他们`detachFromParent()`，并放入`mAttachedScrap`集合
	* 紧接着从`mAttachedScrap`集合取出`ViewHolder`重新`attachToParent*()`
* 紧接着

### mCachedViews
* 用于移出屏幕`Item`的回收和复用，且只能用于指定位置的`Item`
* 有点像`回收池预备队列`，即总是先回收到`mCachedViews`，当它放不下的时候，按照FIFO原则将最先进入的`ViewHolder`存入`mRecyclerPool`

### mViewCacheExtension
### mRecyclerPool
* 用于移出屏幕`Item`的回收和复用，且只能用于指定viewType的表项

## Recyclerview使用优化
1. 降低Item的布局深度，减少绘制计算时间
2. 不要在`onBindViewHolder()`中创建实例，避免频繁GC，占用绘制时间
3. 不要在`onBindViewHolder()`中执行耗时操作，占用绘制时间
4. 懒加载，在滑动过程中不执行`onBindViewHolder`中的代码，避免2、3
5. 共用缓存View：`RecyclerView.setRecycledViewPool(pool)`，如果LayoutManager是LinearLayoutManager或其子类，需要手动开启这个特性：layout.setRecycleChildrenOnDetach(true)
6. 使用`RecyclerView.setHasFixedSize(true)`固定`RecyclerView`高度，Item数量变化时也不会触发`RecyclerView.requestLayout()`：
	* `onItemRangeChanged()`
	* `onItemRangeInserted()`
	* `onItemRangeRemoved()`
	* `onItemRangeMoved()`
7. 空间换时间，修改缓存大小：
	* `setItemViewCacheSize()`：设置`mCachedViews`缓存数量
	* `setMaxRecycledViews()`：设置`mRecyclerPool`缓存数量