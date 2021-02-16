[TOC]

## BitmapFactory
方法名 | 参数及解释
--- | ---
decodeByteArray(byte[] data, int offset, int length) | 从指定字节数组的offset位置开始，将长度为length的数据解析成位图
decodeFile(String pathName) | 从pathName对应的文件解析成的位图对象
decodeFileDescriptor(FileDescriptor fd) | 从FileDescriptor中解析成的位图对象
decodeResource(Resource res,int id) | 根据给定的资源Id解析成位图
decodeStream(InputStream in) | 把输入流解析成位图

## BitmapFactory.options
> BitmapFactory.Options类是BitmapFactory对图片进行解码时使用的一个配置参数类

option | 备注
:---: | :---:
inJustDecodeBounds | 设置为true则不返回Bitmap，用于获取图片宽高等信息，但不加载图片
inSampleSize | 缩放倍数(整数)
inTargetDensity | 设备像素密度 
inDensity | 图片像素密度(资源图片才有) 
inScreenDensity | 默认为0，貌似用不到
inScaled | 默认为true，表示缩放，bitmap.density取目标设备像素密度(inTargetDensity)，false为不缩放，bitmap.density取图片像素密度(inDensity) 
inPreferredConfig | 颜色格式，RGB_565(有透明度图片无效)、ARGB_8888 
inBitmap | 复用Bitmap，配合inMutable使用
inMutable | true才能使用inBitmap
inDither | 是否采用抖动解码，降低图片颜色模式位数时，为防止色彩断带，采用随机噪声色来填充，缓解断带情况

## Bitmap.createBitmap()
方法名 | 用法说明
--- | ---
createBitmap(Bitmap src) | 复制位图
createBitmap(Bitmap src,int x ,int y,int w,int h) | 从源位图src的指定坐标(x,y)开始，截取宽w，高h的部分，用于创建新的位图对象
createScaledBitmap(Bitmap src,int w ,int h,boolean filter) | 对源位图src缩放成宽为w，高为h的新位图
createBitmap(int w ,int h,Bitmap.Config config) | 创建一个宽w，高h的新位图（config为位图的内部配置枚举类）
createBitmap(Bitmap src,int x ,int y,int w,int h,Matrix m,boolean filter) | 从源位图src的指定坐标(x,y)开始，截取宽w，高h的部分，按照Matrix变换创建新的位图对象
### bitmap.copy()
* 可以用来将PNG的颜色格式强转正RGB_565，但透明背景会用白色或黑色填充

### bitmap.compress()
```java
FileOutputStream outputStream = new FileOutputStream(new File(getExternalFilesDir(null), "a.png"));
bitmap.compress(Bitmap.CompressFormat.PNG, 30, outputStream);
outputStream.close();
```
* 用来压缩图片文件大小，加载到内存中的大小不会变化
* 第二个参数，压缩比越低，文件越小
* 第一个参数如果是PNG，则第二个参数压缩比将失效

## 常见问题
### getByteCount()与getAllocationByteCount()的区别
* 不复用Bitmap的情况下两者返回值相等，`API19`开始允许复用更大的Bitmap，此时`getByteCount()`返回新图占用大小，而`getAllocationByteCount()`表示被复用的Bitmap的大小

### 计算Bitmap内存占用
```java
if (density != 0 && targetDensity != 0 && density != screenDensity) {
	scale = (float) targetDensity / density;
}

scaledWidth = int(scaledWidth * scale + 0.5f);
scaledHeight = int(scaledHeight * scale + 0.5f);
byteCount = scaleWidth * scaleHeight * 颜色格式
```

## API变动
### API10之前
* Bitmap自身在Dalvik Heap中，像素在Native中
* 好处是像素不占用Java层的内存，坏处是Native回收时机无法跟Java层很同步

### API10开始
* 像素也放在Dalvik Heap中
* 可调用`recycle()`回收`Bitmap`内存

### API19之前
* `API11`到`API18`，Bitmap复用的图片格式必须是png或jpg，且size、inSampleSize、inPreferConfig都要相同
* `API19`及之后，被复用的`Bitmap`只要大小>=图片大小即可

### API26开始
* 像素放回在Native中，因为解决了Java层Bitmap回收后及时同步Native层回收像素的问题

## Bitmap转换
### drawableRes转Bitmap
`BitmapFactory.decodeResource(getResources(), drawableResId)`

### Bitmap转byte[]
`bitmap.compress(Bitmap.CompressFormat.PNG, 100, baos)`

### byte[]转Bitmap
`BitmapFactory.decodeByteArray(btyes, 0, b.length)`

## Bitmap&Drawable区别
> Drawable作为Android平下通用的图形对象，它可以装载常用格式的图像，比如GIF、PNG、JPG，当然也支持Bitmap，当然还提供一些高级的可视化对象，比如渐变、图形等。Bitmap是Drawable的子集。

对比项 | 显示清晰度 | 占用内存 | 支持缩放 | 支持色相色差调整 | 支持旋转 | 支持透明色 | 绘制速度 | 支持像素操作
:---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---:
Bitmap | 相同 | 大 | 是 | 是 | 是 | 是 | 慢 | 是
Drawable | 相同 | 小 | 是 | 否 | 是 | 是 | 快 | 否

## jpg与png
* 区别：jpg没有alpha通道
* 色彩丰富的用jpg，色彩单一的用png
* jpg是有损压缩，解压更耗时一点

## 转换Bitmap颜色格式
### 方式一
设置`inPreferConfig`，不过注意如果是带有透明度的`png`图片转换为`RGB_565`会失败

### 方式二
```java
Bitmap dstBitmap = Bitmap.createBitmap(640, 360, Bitmap.Config.RGB_565);
Canvas canvas = new Canvas(dstBitmap);
canvas.drawColor(0xffffffff);
canvas.drawBitmap(srcBitmap, 0, 0, null);
```
### 方式三
```java
Bitmap dstBitmap = srcBitmap.copy(Config.RGB_565, false);
```

## 常用效果实现
### 倒影
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

### 大图小用用采样，小图大用用矩阵
```java
Matrix matrix = new Matrix();
matrix.preScale(2, 2, 0f, 0f);
// 如果使用直接替换矩阵的话，在Nexus6 5.1.1上必须关闭硬件加速
// setMatrix方法可能会有绘制问题
canvas.concat(matrix);
canvas.drawBitmap(bitmap, 0,0, paint);
```
```java
Matrix matrix = new Matrix();
matrix.preScale(2, 2, 0, 0);
canvas.drawBitmap(bitmap, matrix, paint);
```

### 缩放
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

### 大图加载
> 官方建议先用`inSampleSize`以2的倍数缩放，然后再用`density`及`targetDensity`缩放到精确大小

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
```java
options.inScaled = true;
options.inSampleSize = 8;
options.inDensity = srcWidth;
options.inTargetDensity = dstWidth * options.inSampleSize;
bitmap = BitmapFactory.decodeFile(fileName, options);
```

