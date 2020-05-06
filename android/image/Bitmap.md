[TOC]

### Bitmap转换
##### drawableRes转Bitmap
`BitmapFactory.decodeResource(getResources(), drawableResId)`

##### Bitmap转byte[]
`bitmap.compress(Bitmap.CompressFormat.PNG, 100, baos)`

##### byte[]转Bitmap
`BitmapFactory.decodeByteArray(btyes, 0, b.length)`

##### Bitmap转Drawable
`new BitmapDrawable(getResources(), bitmap)`

##### Drawable转Bitmap
```java
BitmapDrawable bitmapDrawable = (BitmapDrawable) drawable;
Bitmap bitmap = bitmapDrawable.getBitmap();
```
* Drawable转Bitmap(另一种)：
```java
// 获取drawable长宽
int width = drawable.getIntrinsicWidth();
int height = drawable.getIntrinsicHeight();
drawable.setBounds(0, 0, width, height);

// 获取drawable的颜色格式
Bitmap.Config config = drawable.getOpacity() != PixelFormat.OPAQUE ? Bitmap.Config.ARGB_8888 : Bitmap.Config.RGB_565;
// 创建bitmap
Bitmap bitmap = Bitmap.createBitmap(width, height, config);
// 创建bitmap画布
Canvas canvas = new Canvas(bitmap);
// 将drawable 内容画到画布中
drawable.draw(canvas);
```

### Bitmap&Drawable区别
> Drawable作为Android平下通用的图形对象，它可以装载常用格式的图像，比如GIF、PNG、JPG，当然也支持Bitmap，当然还提供一些高级的可视化对象，比如渐变、图形等。Bitmap是Drawable的子集。

对比项 | 显示清晰度 | 占用内存 | 支持缩放 | 支持色相色差调整 | 支持旋转 | 支持透明色 | 绘制速度 | 支持像素操作
:---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---:
Bitmap | 相同 | 大 | 是 | 是 | 是 | 是 | 慢 | 是
Drawable | 相同 | 小 | 是 | 否 | 是 | 是 | 快 | 否

### BitmapFactory
方法名 | 参数及解释
--- | ---
decodeByteArray(byte[] data, int offset, int length) | 从指定字节数组的offset位置开始，将长度为length的数据解析成位图
decodeFile(String pathName) | 从pathName对应的文件解析成的位图对象
decodeFileDescriptor(FileDescriptor fd) | 从FileDescriptor中解析成的位图对象
decodeResource(Resource res,int id) | 根据给定的资源Id解析成位图
decodeStream(InputStream in) | 把输入流解析成位图

### BitmapFactory.options
> BitmapFactory.Options类是BitmapFactory对图片进行解码时使用的一个配置参数类

参数 | 含义
--- | :---:
inPreferredConfig | 颜色格式，只有JPG可能会生效，RGB_565、ALPHA_8、ARGB_8888 
inBitmap | 复用Bitmap，配合inMutable使用 
inMutable | true才能使用inBitmap
inJustDecodeBounds | 设置为true则不返回Bitmap，用于获取图片宽高等信息，但不加载图片
inSampleSize | 缩放倍数(整数)
inDensity | 位图使用的像素密度，配合inScaled使用，inDensity / 屏幕dpi = inSampleSize
inTargetDesity |  设备的屏幕密度

### Bitmap.createBitmap()
方法名 | 用法说明
--- | ---
createBitmap(Bitmap src) | 复制位图
createBitmap(Bitmap src,int x ,int y,int w,int h) | 从源位图src的指定坐标(x,y)开始，截取宽w，高h的部分，用于创建新的位图对象
createScaledBitmap(Bitmap src,int w ,int h,boolean filter) | 对源位图src缩放成宽为w，高为h的新位图
createBitmap(int w ,int h,Bitmap.Config config) | 创建一个宽w，高h的新位图（config为位图的内部配置枚举类）
createBitmap(Bitmap src,int x ,int y,int w,int h,Matrix m,boolean filter) | 从源位图src的指定坐标(x,y)开始，截取宽w，高h的部分，按照Matrix变换创建新的位图对象

### 转换Bitmap颜色格式
#### 方式一
```java
Bitmap dstBitmap = Bitmap.createBitmap(640, 360, Bitmap.Config.RGB_565);
Canvas canvas = new Canvas(dstBitmap);
canvas.drawBitmap(srcBitmap, 0, 0, null);
```
#### 方式二
```java
Bitmap dstBitmap = srcBitmap.copy(Config.RGB_565, false);
```

### Bitmap重用
> Android3.0(11)开始引入`BitmapFactory.Options.inBitmap`字段，用来重用Bitmap。不过注意，Android4.4(19)开始，才能复用比当前需要大的Bitmap，之前只能使用一样大小的Bitmap。

### 常用效果实现
#### 倒影
```java
   public static Bitmap CreateReflectionImageWithOrigin(Bitmap bitmap) {

        final int reflectionGap = 4;
        int w = bitmap.getWidth();
        int h = bitmap.getHeight();
        Matrix matrix = new Matrix();
        matrix.preScale(1, -1);

        Bitmap reflectionImage = Bitmap.createBitmap(bitmap, 0, h / 2, w,
                h / 2, matrix, false);

        Bitmap bitmapWithReflection = Bitmap.createBitmap(w, (h + h / 2),
                Bitmap.Config.ARGB_8888);
        Canvas canvas = new Canvas(bitmapWithReflection);
        canvas.drawBitmap(bitmap, 0, 0, null);
        Paint deafalutPaint = new Paint();
        canvas.drawRect(0, h, w, h + reflectionGap, deafalutPaint);

        canvas.drawBitmap(reflectionImage, 0, h + reflectionGap, null);

        Paint paint = new Paint();
        LinearGradient shader = new LinearGradient(0, bitmap.getHeight(), 0,
                bitmapWithReflection.getHeight() + reflectionGap, 0x70ffffff,
                0x00ffffff, Shader.TileMode.CLAMP);
        paint.setShader(shader);
        // Set the Transfer mode to be porter duff and destination in
        paint.setXfermode(new PorterDuffXfermode(PorterDuff.Mode.DST_IN));
        // Draw a rectangle using the paint with our linear gradient
        canvas.drawRect(0, h, w, bitmapWithReflection.getHeight()
                + reflectionGap, paint);
        return bitmapWithReflection;
    }
```

#### 圆角
```java
int roundPx = 0;
int width = bitmap.getWidth();
int heigh = bitmap.getHeight();
// 创建输出bitmap对象
Bitmap outmap = Bitmap.createBitmap(width, heigh, Bitmap.Config.ARGB_8888);
Canvas canvas = new Canvas(outmap);
final int color = 0xff424242;
final Paint paint = new Paint();
final Rect rect = new Rect(0, 0, width, heigh);
final RectF rectf = new RectF(rect);
paint.setAntiAlias(true);
canvas.drawARGB(0, 0, 0, 0);
paint.setColor(color);
canvas.drawRoundRect(rectf, roundPx, roundPx, paint);
paint.setXfermode(new PorterDuffXfermode(PorterDuff.Mode.SRC_IN));
canvas.drawBitmap(bitmap, rect, rect, paint);
```

#### 缩放
```java
int newWidth = 960;
int newHeight = 540;
int oldWidth = originalBitmap.getWidth();
int oldHeight = originalBitmap.getHeight();
Matrix matrix = new Matrix();
matrix.postScale((float) newWidth / oldWidth, (float) newHeight / oldHeight);
Bitmap scaleBitmap = Bitmap.createBitmap(originalBitmap, 0, 0, oldWidth, oldHeight, matrix, true);
ImageView imageView = findViewById(R.id.activity_main_a_iv);
imageView.setImageBitmap(scaleBitmap);
```

#### 大图加载
```java
BitmapFactory.Options options = new BitmapFactory.Options();
options.inJustDecodeBounds = true;
BitmapFactory.decodeResource(getResources(), R.drawable.bg_launcher_default, options);

int inSampleSize = calculateInSampleSize(options, options.outWidth / 4, options.outHeight / 4);
options.inJustDecodeBounds = false;
options.inSampleSize = inSampleSize;

Bitmap bitmap = BitmapFactory.decodeResource(getResources(), R.drawable.bg_launcher_default, options);

private int calculateInSampleSize(BitmapFactory.Options justDecodeBoundsOptions, int dstWidth, int dstHeight) {
    int inSampleSize = 1;
    while (justDecodeBoundsOptions.outWidth / inSampleSize > dstWidth
            && justDecodeBoundsOptions.outHeight / inSampleSize > dstHeight
    ) {
        inSampleSize *= 2;
    }
    return inSampleSize;
}
```