[TOC]

> RecyclerView在24.2.0版本中新增了SnapHelper这个辅助类，用于辅助RecyclerView在滚动结束时将Item对齐到某个位置。

#### LinearSnapHelper
> 可以让RecyclerView滚动停止时相应的Item停留中间位置。

#### PagerSnapHelper
> 可以使RecyclerView像ViewPager一样的效果，一次只能滑一页，而且居中显示。

#### Fling操作
> 手指在屏幕上滑动RecyclerView然后松手，RecyclerView中的内容会顺着惯性继续滚动直到停止。从手指离开屏幕瞬间，到滚动停止的过程叫Fling。

#### findTargetSnapPosition()
* 该方法会根据触发Fling操作的速率(参数velocityX和参数velocityY)来找到RecyclerView需要滚动到哪个位置，该位置对应的ItemView就是那个需要进行对齐的列表项。
* 我们把这个位置称为targetSnapPosition，对应的View称为targetSnapView。
* 如果找不到targetSnapPosition，就返回RecyclerView.NO_POSITION。

####  findSnapView()
* 该方法会找到当前layoutManager上最接近对齐位置的那个view，该view称为SanpView，对应的position称为SnapPosition。
* 如果返回null，表示没有需要对齐的View，不会做滚动对齐调整。

#### calculateDistanceToFinalSnap()
> 这个方法会计算第二个参数对应的ItemView当前的坐标与需要对齐的坐标之间的距离。

![](https://github.com/gxd523/note/raw/master/pic/calculateDistanceToFinalSnap.png)

#### snapToTargetExistingView()

> 该方法的作用是对SnapView进行滚动调整，以使得SnapView达到对齐效果。