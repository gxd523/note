* `content://com.github.provider/abc-files-path/a.apk`
* `file:///storage/emulated/0/Android/data/com.github/files/abc/a.apk`
* `Android7.0(API 24)`，禁止通过`Intent`直接传递文件路径，需要通过`FileProvider`转换路径，以隐藏真实路径
* `Android8.0(API 26)`安装未知应用需要权限，并在`onActivityResult()`中安装

```xml
<paths>
    <external-files-path
        name="abc-files-path"
        path="abc/" />
</paths>
```

```xml
<provider
    android:name="androidx.core.content.FileProvider"
    android:authorities="${applicationId}.ApkProvider"
    android:exported="false"
    android:grantUriPermissions="true">
    <meta-data
        android:name="android.support.FILE_PROVIDER_PATHS"
        android:resource="@xml/provider_paths" />
</provider>
```

```kotlin
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
    if (packageManager.canRequestPackageInstalls()) {
        installApk()
    } else {
        val parse = Uri.parse("package:$packageName")
        val intent = Intent(Settings.ACTION_MANAGE_UNKNOWN_APP_SOURCES, parse)
        // 别忘在onActivityResult()中回调installApk()
        startActivityForResult(intent, 123)
    }
} else {
    installApk()
}

fun installApk() {
    val intent = Intent(Intent.ACTION_VIEW)
    intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK)
    val path: String = "${getExternalFilesDir("abc")}/a.apk"
    val data: Uri
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.N) {
        data = FileProvider.getUriForFile(this, "$packageName.ApkProvider", File(path))
        intent.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION)
    } else {
        data = Uri.parse("file://$path")
    }
    intent.setDataAndType(data, "application/vnd.android.package-archive")
    startActivity(intent)
}
```