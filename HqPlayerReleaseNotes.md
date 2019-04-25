# Release notes #

### 1.4.10 ###

* 添加`HqVideoViewListener`，使用者可通过HqVideoView.setHqVideoViewListener()注册监听，并实现自己需要的方法。

### 1.4.9 ###

* 内部依赖播放器ExoPlayer升级到2.9.3。

### 1.4.8 ###

* 增加`HqMediaPlayerManager.setScaleType()`设置视频比例模式，分别为`HqScaleType.CENTER`、`HqScaleType.CENTER_CROP`、`HqScaleType.STRETCH`。
* 调用`HqVideoView.attachPlayerAndPlay()`前，不再需要调用`HqVideoView.stopVideo()`。

### 1.4.7 ###

* 播放器设置渲染控件的方式改为`HqMediaPlayerManager.setRenderType()`，分别可设置为`HqRenderType.SURFACE_VIEW`、`HqRenderType.TEXTURE_VIEW`。

### 1.4.6 ###

* 内部依赖播放器ExoPlayer升级到2.8.1。

### 1.4.5 ###

* 添加`HqVideoView.takeScreenshot()`，可截取视频画面(**目前仅支持`HqRenderType.TEXTURE_VIEW`渲染方式**)。
* 添加`HqPlayerState.PLAYER_STATE_ERROR`播放状态，并在错误状态时重新播放(**错误状态是由所依赖播放器回调的，有些黑屏等播放异常可能不会回调**)。
* 内部依赖播放器ExoPlayer升级到2.8.0。
* 规范播放状态命名及相关方法命名(**没有向下兼容！**)