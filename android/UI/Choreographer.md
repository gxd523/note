[TOC]

## 丢帧的原因
* 绘制View的时间超过16.6ms
* 主线程在处理耗时消息，View绘制的Message没有在这一帧内执行

## 监听帧率问题

```java
public class FPSFrameCallback implements Choreographer.FrameCallback {
    private static final String TAG = "FPS_TEST";
    private long mLastFrameTimeNanos;
    private final long perFrameNanos;

    public FPSFrameCallback(long lastFrameTimeNanos) {
        mLastFrameTimeNanos = lastFrameTimeNanos;
        perFrameNanos = (long) (1000000000 / 60.0);
    }

    @Override
    public void doFrame(long frameTimeNanos) { //Vsync信号到来的时间frameTimeNanos
        //初始化时间
        if (mLastFrameTimeNanos == 0) {
            //上一帧的渲染时间
            mLastFrameTimeNanos = frameTimeNanos;
        }
        final long jitterNanos = frameTimeNanos - mLastFrameTimeNanos;
        if (jitterNanos >= perFrameNanos) {
            final long skippedFrames = jitterNanos / perFrameNanos;
            if (skippedFrames > 5) {
                Log.d(TAG, "Skipped " + skippedFrames + " frames!  "
                    + "The application may be doing too much work on its main thread.");
            }
        }
        mLastFrameTimeNanos = frameTimeNanos;
        //注册下一帧回调
        Choreographer.getInstance().postFrameCallback(this);
    }
}
```

