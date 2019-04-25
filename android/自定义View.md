[TOC]

### 坐标、角度、弧度
* View相对于父控件的坐标getLeft()、getTop()、getRight()、getBottom()
* MotionEvent中，getX()表示以父控件为坐标系，getRawX()表示以屏幕为坐标系
* 角度是60进制，弧度是10进制
* 角度、弧度关系：π=180
* 屏幕坐标系中角度增大方向为顺时针
### 颜色
颜色模式 | 备注
--- | ---
ARGB8888 | 四通道高精度(32位)
ARGB4444 | 四通道低精度(16位)
RGB565 | 屏幕默认模式(16位)
Alpha8 | 仅有透明通道(8位)
> 我们常用的是ARGB8888和ARGB4444，而在所有的安卓设备屏幕上默认的模式都是RGB565,请留意这一点。

默认情况下，当一个颜色绘制到Canvas上时的混合模式是这样计算的：
		**(RGB通道) 最终颜色 = 绘制的颜色 + (1 - 绘制颜色的透明度) × Canvas上的原有颜色。**

注意：
* 这里我们一般把每个通道的取值从0(0x00)到255(0xff)映射到0到1的浮点数表示。
* 这里等式右边的“绘制的颜色”、“Canvas上的原有颜色” 都是经过预乘了自己的Alpha通道的值。如绘制颜色：0x88ffffff，那么参与运算时的每个颜色通道的值不是1.0，而是(1.0 * 0.5333 = 0.5333)。 (其中0.5333 = 0x88/0xff)

使用这种方式的混合，就会造成后绘制的内容以半透明的方式叠在上面的视觉效果。

其实还可以有不同的混合模式供我们选择，用Paint.setXfermode，指定不同的PorterDuff.Mode。

下表是各个PorterDuff模式的混合计算公式：（D指原本在Canvas上的内容dst，S指绘制输入的内容src，a指alpha通道，c指RGB各个通道）
混合模式 | 计算公式
--- | ---
ADD | Saturate(S + D)
CLEAR | [0, 0]
DARKEN | [Sa + Da - SaDa, Sc(1 - Da) + Dc*(1 - Sa) + min(Sc, Dc)]
DST | [Da, Dc]
DST_ATOP | [Sa, Sa * Dc + Sc * (1 - Da)]
DST_IN | [Sa * Da, Sa * Dc]
DST_OUT | [Da * (1 - Sa), Dc * (1 - Sa)]
DST_OVER | [Sa + (1 - Sa)Da, Rc = Dc + (1 - Da)Sc]
LIGHTEN | [Sa + Da - SaDa, Sc(1 - Da) + Dc*(1 - Sa) + max(Sc, Dc)]
MULTIPLY | [Sa * Da, Sc * Dc]
SCREEN | [Sa + Da - Sa * Da, Sc + Dc - Sc * Dc]
SRC | [Sa, Sc]
SRC_ATOP | [Da, Sc * Da + (1 - Sa) * Dc]
SRC_IN | [Sa * Da, Sc * Da]
SRC_OUT | [Sa * (1 - Da), Sc * (1 - Da)]
SRC_OVER | [Sa + (1 - Sa)Da, Rc = Sc + (1 - Sa)Dc]
XOR | [Sa + Da - 2 * Sa * Da, Sc * (1 - Da) + (1 - Sa) * Dc]

用示例图来查看使用不同模式时的混合效果如下（src表示输入的图，dst表示原Canvas上的内容）：
![示例图](http://ww4.sinaimg.cn/large/005Xtdi2gw1f1wa0f0mzjj30hh0fsjt8.jpg)

### View绘制流程

![](http://ww4.sinaimg.cn/large/005Xtdi2jw1f638wreu74j30fc0heaay.jpg)

步骤 | 关键字 | 作用
--- |--- | ---
1 | 构造函数 | View初始化
2 | onMeasure | 测量View大小
3 | onSizeChanged | 确定View大小
4 | onLayout | 确定子View布局(自定义View包含子View时有用)
5 | onDraw | 实际绘制内容
6 | 提供接口 | 控制View或监听View某些状态。

> 有三个参数的构造函数中第三个参数是默认的Style，这里的默认的Style是指它在当前Application或Activity所用的Theme中的默认Style，且只有在明确调用的时候才会生效。**注意：这个style指的不是View中的Style属性**

##### onMeasure()
> View的大小不仅由自身所决定，同时也会受到父控件的影响，为了我们的控件能更好的适应各种情况，一般会自己进行测量。
模式 | 二进制数值 | 描述
--- | --- | ---
UNSPECIFIED | 00 | 默认值，父控件没有给子view任何限制，子View可以设置为任意大小。
EXACTLY | 01 | 表示父控件已经确切的指定了子View的大小。
AT_MOST | 10 | 表示子View具体大小没有尺寸限制，但是存在上限，上限一般为父View大小。
> 在int类型的32位二进制位中，31-30这两位表示测量模式,29~0这三十位表示宽和高的实际值。

> **如果对View的宽高进行修改了，不要调用 super.onMeasure( widthMeasureSpec, heightMeasureSpec); 要调用 setMeasuredDimension( widthsize, heightsize); 这个函数。**

##### onSizeChanged()

> 这个函数在视图大小发生改变时调用。

Q: 在测量完View并使用setMeasuredDimension函数之后View的大小基本上已经确定了，那么为什么还要再次确定View的大小呢？

A: 这是因为View的大小不仅由View本身控制，而且受父控件的影响，所以我们在确定View大小的时候最好使用系统提供的onSizeChanged回调函数。

这个函数比较简单，我们只需关注 宽度(w), 高度(h) 即可，这两个参数就是View最终的大小。
##### onLayout()
> 确定布局的函数是onLayout，它用于确定子View的位置，在自定义ViewGroup中会用到，他调用的是子View的layout函数。

在自定义ViewGroup中，onLayout一般是循环取出子View，然后经过计算得出各个子View位置的坐标值，然后用以下函数设置子View位置。
```
child.layout(l, t, r, b);
```
##### onDraw()
> onDraw是实际绘制的部分，也就是我们真正关心的部分，使用的是Canvas绘图。

### Canvas
Canvas的常用操作速查表
操作类型 | 相关API | 备注
--- | --- | ---
绘制颜色 | drawColor, drawRGB, drawARGB | 使用单一颜色填充整个画布
绘制基本形状 | drawPoint, drawPoints, drawLine, drawLines, drawRect, drawRoundRect, drawOval, drawCircle, drawArc | 依次为 点、线、矩形、圆角矩形、椭圆、圆、圆弧
绘制图片 | drawBitmap, drawPicture | 绘制位图和图片
绘制文本 | drawText, drawPosText, drawTextOnPath | 依次为 绘制文字、绘制文字时指定每个文字位置、根据路径绘制文字
绘制路径 | drawPath | 绘制路径，绘制贝塞尔曲线时也需要用到该函数
顶点操作 | drawVertices, drawBitmapMesh | 通过对顶点操作可以使图像形变，drawVertices直接对画布作用、 drawBitmapMesh只对绘制的Bitmap作用
画布剪裁 | clipPath, clipRect | 设置画布的显示区域
画布快照 | save, restore, saveLayerXxx, restoreToCount, getSaveCount | 依次为 保存当前状态、 回滚到上一次保存的状态、 保存图层状态、 回滚到指定状态、 获取保存次数
画布变换 | translate, scale, rotate, skew | 依次为 位移、缩放、 旋转、错切
Matrix(矩阵) | getMatrix, setMatrix, concat | 实际上画布的位移，缩放等操作的都是图像矩阵Matrix， 只不过Matrix比较难以理解和使用，故封装了一些常用的方法。

> 为什么会有Rect和RectF两种？两者最大的区别就是精度不同，Rect是int(整形)的，而RectF是float(单精度浮点型)的。

> **所有的画布操作都只影响后续的绘制，对之前已经绘制过的内容没有影响**
> 
##### 位移(translate)
* translate是坐标系的移动，可以为图形绘制选择一个合适的坐标系。 请注意，位移是基于当前位置移动，而不是每次基于屏幕左上角的(0,0)点移动
##### 缩放比例(sx,sy)取值范围详解：
取值范围(n) | 说明
--- | ---
(-∞, -1) | 先根据缩放中心放大n倍，再根据中心轴进行翻转
-1 | 根据缩放中心轴进行翻转
(-1, 0) | 先根据缩放中心缩小到n，再根据中心轴进行翻转
0 | 不会显示，若sx为0，则宽度为0，不会显示，sy同理
(0, 1) | 根据缩放中心缩小到n
1 | 没有变化
(1, +∞) | 根据缩放中心放大n倍

> 坐标原点移到(px,py)-->进行缩放-->进行翻转(坐标取反)

##### drawPicture
> 使用Picture前请关闭硬件加速，以免引起不必要的问题！

### Paint

