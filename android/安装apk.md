```java
Intent intent = new Intent(Intent.ACTION_VIEW);
intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
intent.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION);

String authority;
//try {
//    ComponentName component = new ComponentName(getPackageName(), FileProvider.class.getName());
//    ProviderInfo providerInfo = getPackageManager().getProviderInfo(component, PackageManager.MATCH_DEFAULT_ONLY);
//    authority = providerInfo.authority;
//} catch (PackageManager.NameNotFoundException e) {
//    e.printStackTrace();
    authority = getPackageName() + ".provider";
//}
Uri uri = FileProvider.getUriForFile(MainActivity.this, authority, apkFile);
intent.setDataAndType(uri, "application/vnd.android.package-archive");

startActivity(intent);
```