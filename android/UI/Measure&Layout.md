[TOC]

## 绘制流程
![](https://gitee.com/hysbtr/pic/raw/master/draw_process.jpeg)

## invalidate()&postInvalidate()
* 只回调`onDraw()`

## requestLayout()
* 

## Measure
* 测量View大小，最终需调用`setMeasuredDimension()`设置宽高
* 如果是ViewGroup，还需调用`measureChildren()`或`measureChild()`
* measureChildren()：测量所有子View
* measureChild()：测量指定View
* getWidth()：在`layout()`后，设置了`mRight`、`mLeft`才有值的
* getMeasureWidth()：在`measure()`后，调用了`setMeasuredDimension()`，也就是设置了`mMeasuredWidth`，才值了

模式 | 二进制 | 描述
:---: | :---: | :---:
UNSPECIFIED | 00 | 表示父容器不对View有任何限制，一般用于系统内部，表示一种测量状态 
EXACTLY | 01 | 父容器已经检测出view所需的精确大小，相当于match_parent或指定具体数值 
AT_MOST | 10 | 表示子View不超过父View大小，相当于wrap_parent 

## Layout
* 摆放View，通常由ViewGroup实现，View不需要

## Draw
* dispatchDraw()：`onDraw()`后调用，里面调用了子View的`draw()`
* ViewGroup默认不执行`onDraw()`，如果复写，需调用`setWillNotDraw(false)`清楚不需要绘制的标记