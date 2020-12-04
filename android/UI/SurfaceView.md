## 独立Surface
* `SurfaceView`可以在非UI线程绘制UI
* 有独立的`Surface`，与`Window`不共用`Surface`
* `SurfaceView`刷新UI时，不需要跟随`Window`自顶向下遍历所有`View`，效率很高
* `SurfaceView`的`Surface`在`Window`的`Surface`的下面
* `SurfaceView`在`Window`的`Surface`上设置一块透明区域以显示`SurfaceView`的Surface
* `Surface`、`SurfaceView`、`SurfaceHolder`三者为MVC关系
* `lockCanvas()`后会用同步锁防止多线程使用`canvas`
* 所以不能进行View的平移的操作

## 双缓存机制
SurfaceView在更新视图时用到了两张Canvas，一张frontCanvas和一张backCanvas，每次实际显示的是frontCanvas，backCanvas存储的是上一次更改前的视图，当使用lockCanvas（）获取画布时，得到的实际上是backCanvas而不是正在显示的frontCanvas，之后你在获取到的backCanvas上绘制新视图，再unlockCanvasAndPost（canvas）此视图，那么上传的这张canvas将替换原来的frontCanvas作为新的frontCanvas，原来的frontCanvas将切换到后台作为backCanvas。例如，如果你已经先后两次绘制了视图A和B，那么你再调用lockCanvas（）获取视图，获得的将是A而不是正在显示的B，之后你讲重绘的C视图上传，那么C将取代B作为新的frontCanvas显示在SurfaceView上，原来的B则转换为backCanvas。

## SurfaceView&TextureView
a | SurfaceView | TextureView
:---: | :---: | :---:
内存 | 低 | 高
绘制 | 及时 | 1-3帧延迟
耗电 | 低 | 高
动画&截图 | 不支持 | 支持