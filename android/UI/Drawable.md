[TOC]

* `Drawable`是一个抽象类
* 子类：`ShapeDrawable`、`BitmapDrawable`、`VectorDrawable`

## 重要方法
### draw(canvas)
* 将`Drawable`的`Shape`或`Bitmap`画出来
* `canvas`可有`View.onDraw(canvas)`提供

### invalidateSelf()

## Drawable转Bitmap
### BitmapDrawable可直接获取Bitmap
```java
BitmapDrawable bitmapDrawable = (BitmapDrawable) drawable;
Bitmap bitmap = bitmapDrawable.getBitmap();
```

### 其他类型Drawable转Bitmap
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

## Bitmap转Drawable
`new BitmapDrawable(getResources(), bitmap)`