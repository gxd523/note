#### odex转smali
* 从Android系统的`/system/framework/arm`中pull出`boot.oat`文件或所有文件
* [下载baksmali.jar文件](https://bitbucket.org/JesusFreke/smali/downloads/)
* 将`baksmali.jar`、`boot.oat`、`odex文件`放在同一目录下，cmd进入此目录，输入`java -jar baksmali.jar deodex a.odex`，目录中生成out目录，里面为smali文件

#### smali转dex
* [下载smali.jar文件](https://bitbucket.org/JesusFreke/smali/downloads/)
* 将下载好的`smali.jar`文件放入刚才的目录中，cmd继续输入`java -jar smali-2.4.0.jar assemble out -o  a.dex`，目录中生成a.dex文件

