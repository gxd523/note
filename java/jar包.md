
### java命令踩坑指南
* `java`命令后跟的是执行类名称(如：Hello而不是Hello.class)
* 执行类带包名的，要写执行类的完整名称(如：com.jerry.Hello)
* `java`命令后也不是class文件路径，所以执行`java`命令的当前目录与执行类的完整名称要保持一致
* 如：com.jerry.Hello，`Hello.class`文件要放在`com/jerry`目录下，`java`命令要在`com`目录所在的目录执行

### 打jar包
* 命令行进入`src`目录(为了生成包名目录)，`javac <java文件> -d <生成class文件路径>`
	* `Java文件`：写main函数的那个java文件就好
	* `class文件目录`：要提前创建好
* 创建`META-INF/MENIFEST.MF`目录及文件(注意别跟com同目录)，设置好主清单属性：
	* 注意文件最底下要留换行
    ```
    Manifest-Version: 1.0
    Created-By: 1.8.0_121 (Oracle Corporation)
    Main-Class: com.jerry.Main

    ```
* `jar -cvfm a.jar <META-INF/MENIFEST.MF路径> *`(`*`表示当前目录里的所有class文件)
	
参数 | 含义
:--: | :--
-c | 创建一个jar包
-t | 显示jar中的内容列表
-x | 解压jar包
-u | 添加文件到jar包中
-f | 指定jar包的文件名
-v | 控制台输出相关信息 
-m | 指定MANIFEST.MF文件 
-0 | 产生jar包时不对其中的内容进行压缩处理
-M | 不产生所有文件的清单文件。这个参数与忽略掉-m参数的设置 
-i | 为指定的jar文件创建索引文件
-C | 在相应的目录下执行jar命令,相当于cd到那个目录，然后不带-C执行jar命令
* `java -jar a.jar`运行
#### 打出的jar包引用其他jar包
* `javac -cp <jar包路径> <java文件路径> -d <class文件绝对路径>`，`-cp`即`-classpath`
* 同样创建MENIFEST文件，但要加jar包路径`Class-Path: b.jar`
* 打出的jar包中并没有b.jar，所以执行jar包时，要注意执行类与b.jar的目录关系
* 虽然可以将`b.jar`打入jar，但如何引用比较复杂