[原文出处](https://www.jianshu.com/p/d11892bbe055)
```
public Xfermode setXfermode(Xfermode xfermode) { 
	long xfermodeNative = 0; 
	if (xfermode != null) {
		xfermodeNative = xfermode.native_instance; 
	}
	native_setXfermode(mNativePaint, xfermodeNative); 	mXfermode = xfermode; 
	return xfermode; 
}
```

```
public enum Mode {
    /** [0, 0] */
    CLEAR (0),
    /** [Sa, Sc] */
    SRC (1),
    /** [Da, Dc] */
    DST (2),
    /** [Sa + (1 - Sa)*Da, Rc = Sc + (1 - Sa)*Dc] */
    SRC_OVER (3),
    /** [Sa + (1 - Sa)*Da, Rc = Dc + (1 - Da)*Sc] */
    DST_OVER (4),
    /** [Sa * Da, Sc * Da] */
    SRC_IN (5),
    /** [Sa * Da, Sa * Dc] */
    DST_IN (6),
    /** [Sa * (1 - Da), Sc * (1 - Da)] */
    SRC_OUT (7),
    /** [Da * (1 - Sa), Dc * (1 - Sa)] */
    DST_OUT (8),
    /** [Da, Sc * Da + (1 - Sa) * Dc] */
    SRC_ATOP (9),
    /** [Sa, Sa * Dc + Sc * (1 - Da)] */
    DST_ATOP (10),
    /** [Sa + Da - 2 * Sa * Da, Sc * (1 - Da) + (1 - Sa) * Dc] */
    XOR (11),
    /** [Sa + Da - Sa*Da, Sc*(1 - Da) + Dc*(1 - Sa) + min(Sc, Dc)] */
    DARKEN (12),
    /** [Sa + Da - Sa*Da, Sc*(1 - Da) + Dc*(1 - Sa) + max(Sc, Dc)] */
    LIGHTEN (13),
    /** [Sa * Da, Sc * Dc] */
    MULTIPLY (14),
    /** [Sa + Da - Sa * Da, Sc + Dc - Sc * Dc] */
    SCREEN (15),
    /** Saturate(S + D) */
    ADD (16),
    OVERLAY (17);
    Mode(int nativeInt) {
        this.nativeInt = nativeInt;
    }
    /**
     * @hide
     */
    public final int nativeInt;
}
```

> Sa：全称为Source alpha，表示源图的Alpha通道；
> Sc：全称为Source color，表示源图的颜色；
> Da：全称为Destination alpha，表示目标图的Alpha通道；
> Dc：全称为Destination color，表示目标图的颜色.

```
public class PorterDuffXfermodeView extends View {
    private Paint mPaint;
    private Bitmap dstBmp, srcBmp;
    private RectF dstRect, srcRect;

    private Xfermode mXfermode;

    public PorterDuffXfermodeView(Context context) {
        super(context);
        mPaint = new Paint(Paint.ANTI_ALIAS_FLAG | Paint.FILTER_BITMAP_FLAG);
        dstBmp = BitmapFactory.decodeResource(getResources(), R.drawable.destination);
        srcBmp = BitmapFactory.decodeResource(getResources(), R.drawable.source);
        PorterDuff.Mode mPorterDuffMode = PorterDuff.Mode.SRC_ATOP;
        mXfermode = new PorterDuffXfermode(mPorterDuffMode);
    }

    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        //背景色设为白色，方便比较效果
        canvas.drawColor(Color.WHITE);
        //将绘制操作保存到新的图层，因为图像合成是很昂贵的操作，将用到硬件加速，这里将图像合成的处理放到离屏缓存中进行
        int saveCount = canvas.saveLayer(srcRect, mPaint, Canvas.ALL_SAVE_FLAG);
        //绘制目标图
        canvas.drawBitmap(dstBmp, null, dstRect, mPaint);
        //设置混合模式
        mPaint.setXfermode(mXfermode);
        //绘制源图
        canvas.drawBitmap(srcBmp, null, srcRect, mPaint);
        //清除混合模式
        mPaint.setXfermode(null);
        //还原画布
        canvas.restoreToCount(saveCount);
    }

    @Override
    protected void onSizeChanged(int w, int h, int oldw, int oldh) {
        super.onSizeChanged(w, h, oldw, oldh);
        int width = w <= h ? w : h;
        int centerX = w / 2;
        int centerY = h / 2;
        int quarterWidth = width / 4;
        srcRect = new RectF(centerX - quarterWidth, centerY - quarterWidth, centerX + quarterWidth, centerY + quarterWidth);
        dstRect = new RectF(centerX - quarterWidth, centerY - quarterWidth, centerX + quarterWidth, centerY + quarterWidth);
    }
}
```


