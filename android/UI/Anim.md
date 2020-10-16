[TOC]

### 帧动画(Drawable Animation)
* 设置逐帧图片和时间间隔进行播放形成动画效果
* 不会内存泄漏，但很可能因为图片过大过多导致OOM

### 补间动画(Tween Animation)
* 包含4中效果：平移(Translate)、旋转(Rotate)、缩放(Scale)、透明度(Alpha)
* 只是在视图层实现了动画效果，并没有真正改变View的属性
* 不会导致内存泄漏

### PropertyAnimator属性动画
* 通过不断的改变View的属性，不断的重绘而形成动画效果
* 相比于视图动画，View的属性是真正改变了，且不在局限于4中变化效果，View的属性都可以做动画
* 属性动画分为ObjectAnimator和ValueAnimator，其中ObjectAnimator是继承于ValueAnimator
* 可能导致内存泄漏
* view.clearAnimation()防止GONE失效
* 使用动画的过程中，建议开启硬件加速，这样会提交动画的流畅性。
* Android3.0(API11)才开始支持

#### ValueAnimator
![](https://gitee.com/hysbtr/pic/raw/master/value_anim.png)

* ValueAnimator并不会改变属性的大小，他只是在一段时间生成某些值。我们在值的回调里改变View的属性，进而产生动画效果。
