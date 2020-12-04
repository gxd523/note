## ViewModelProvider
* 持有`ViewModelStore`、`ViewModelFactory`对象

## ViewModelStore
* 一个Activity对应一个`ViewModelStore`，如果旋转过就通过`ConfigurationInstance`获取，否则创建
* 使用`HashMap<String,ViewModel>`存储`ViewModel`
* 通过类名从`HashMap`中拿`ViewModel`，没有才创建，所以每个`ViewModelStore`只能创建一个`ViewModel`具体类的实例

## ViewModelFactory
* `create(modelClass)`：创建ViewModel对象