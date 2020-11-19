## ContentResolver加载资源图片

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

