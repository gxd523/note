* Android 4.3(API 18)推出
* `View`的最上面的一个透明层，在上面添加`View`、`Drawable`不会影响布局结构
* 3个方法：`add(View/Drawable)`、`remove(View/Drawable)`、`clear()`
* ViewOverlay中的view或者drawable不会响应任何触摸事件
* `add(View)`会将添加的`View`放到`ViewOverlay`下，`remove()`、`clear()`会移除
* `add(View/Drawable)`添加的`View`或`Drawable`的坐标是按全屏算的，但显示区域不会超过`ViewOverlay`所在的`View`