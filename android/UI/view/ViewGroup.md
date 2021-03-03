[TOC]

## removeView()&detachViewFromParent(int)区别
* 都会将子View从父View的Child数组中移除，并断开相互引用
* `detachViewFromParent(int)`更轻量，不会触发重绘