[TOC]

## ContentResolver
* 实际是`ApplicationContentResolver`
* 最终通过Binder跨进程调用`ActivityManagerService.getContentProvider()`的`ActivityManagerNative.getDefault().getContentProvider()`
* 

### ContentResolver加载资源图片
```java
Uri uri = Uri.parse(
        ContentResolver.SCHEME_ANDROID_RESOURCE
                + "://"
                + resources.getResourcePackageName(resId)
                + '/'
                + resources.getResourceTypeName(resId)
                + '/'
                + resources.getResourceEntryName(resId)
);
InputStream inputStream = contentResolver.openInputStream(uri);
Bitmap bitmap = BitmapFactory.decodeStream(inputStream);
imageView.setImageBitmap(bitmap);
```

## ContentProvider
* 用来对外共享数据
