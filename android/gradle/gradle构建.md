### implementation&api
* 依赖隔离只对本地依赖有效，远程依赖不会隔离也不需要，因为依赖隔离是为了加快编译速度
* app依赖本地module时，module中的依赖(不管远程还是本地)如果使用`api`，则app也能调用到，如果使用`implementation`，则app调用不到，即依赖被隔离
* 注意跟app使用哪种方式引用本地module没关系，也就是module中以哪种方式引用，影响的是引用module的module及引用module的module的module...

### Task
* `assemble`：组装APK
* `clean`：清理构建生成文件，例如APK
* `check`：运行`Lint`，如果`Lint`发现错误，可终止构建
* `build`：同时运行`assemble`和`check`

#### 常用Task
* 常用Task`connectedCheck`、`deviceCheck`、`installDebug`、`uninstallRelease`

### 追踪错误
* `--stacktrace`

### 其他
* 运行`./gradlew`提示`permission denied`：chmod u=rx gradlew
* gradle本地存放目录：`/Users/guoxiaodong/.gradle/wrapper/dists`
* 远程依赖本地目录：`/Users/guoxiaodong/.gradle/caches/modules-2/files-2.1`
* `targetSdkVersion`：向前兼容的版本，和`compileSdkVersion`无关
* 查看项目中的所有任务：`./gradlew tasks`，加`--all`查看这些任务的依赖关系
* 添加`BuildConfig`参数：`buildConfigField "boolean", "Test_Boolean", "true"`
* gradle中配置资源：`resValue "string", "app_name", "Example Debug"`
* `settings.gradle`中的`:`相当于路径分隔的`/`
* `org.gradle.parallel=true`可以并行构建
* gradlew buildEnvironment
