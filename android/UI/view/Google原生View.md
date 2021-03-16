[TOC]

## CoordinatorLayout

### Behavior
* `CoordinatorLayout`直接子View设置的属性
* 

## NestedScrollView
* 替代`ScrollView`

## AppBarLayout
* LinearLayout的子类
* 子View设置`layout_scrollFlags`：
	* `scroll`：可被滚动出屏幕
	* `enterAlways`：
	* `enterAlwaysCollapsed`：内部的子控件，从大于minHeight或子控件开始显示
	* `exitUntilCollapsed`：小于minHeight或子控件时，按minHeight或子控件显示
	* `snap`：滑动结束时，超过一半显示则完全展开，否则完全折叠

## CollapsingToolbarLayout
* FrameLayout的子类
* `contentScrim`：折叠时工具栏的颜色
* `statusBarScrim`：折叠时状态栏的颜色
* `layout_collapseMode`：子布局Toolbar的3种折叠模式：
	* none：默认属性，布局将正常显示，无折叠行为
	* pin：CollapsingToolbarLayout折叠后，此布局将固定在顶部
	* parallax：CollapsingToolbarLayout折叠时，此布局也会有视差折叠效果

### layout_collapseParallaxMultiplier
* 在滑动折叠时，将要被折叠的视图会被看做两个部分：
	* 下半部分会隐藏在内容视图的下方
	* 上半部分会向上滑出屏幕的上边缘
* layout_collapseParallaxMultiplier：下半部分高度/折叠视图总高度
* 取值范围：0~1.0

## FloatingActionButton
* 在Z轴方向的最上面，没有View能覆盖它