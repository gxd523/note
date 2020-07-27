### Android5.0(21)之前
* 21之前使用Dalvik虚拟机，限制每个APK只能使用一个dex文件，所以要使用multidex库
* gradle文件的android的defaultConfig中添加multiDexEnabled true
* 继承MultiDexApplication
* 