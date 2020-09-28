## DSL
* 编写`Gradle`的`Grovvy`是动态语言，编译时不做检查，所以IDE提示也很差

## 反射
### Java反射
* `Person::class.java`
* `person.javaClass`
* `person::class.java`
* `Class.forName("com.demo.kotlin.data.Person")`
* 有参构造：`clazz.getConstructor(String::class.java).newInstance("a")`
* `val`变量是运行时才能确定值，不是真正的`final`，所以可以反射修改
* 扩展函数在Java中是带有接收者作为参数的静态方法
* 包级函数、扩展函数都在`JavaReflectKt`中，只能通过`Class.forName()`方式调用
* `clazz.getMethod("sayHello", Person::class.java).invoke(null, person)`

### Kotlin反射
* `org.jetbrains.kotlin:kotlin-reflect:1.4.10`
* kotlin反射目前一个是包体积有点大，另一个性能比Java差很多
* `KClass`：`Person::class`、`person::class`、`person.javaClass.kotlin`
* 获取属性：`kClass2.memberProperties.first { it.name == "age" }.get(person)`
* 只有`var`属性可以反射设置值：`(ageProperty as? KMutableProperty1<Person, Int>)?.set(person, 998)`
* 成员函数：`kClass.memberFunctions.first { it.name=="print" }.call(person)`
* `memberExtensionFunctions`是定义在类里面的扩展方法(可以是别的类的扩展方法)
* kotlin目前没法反射包级函数(包括包级扩展方法)
* kotlin注解`Target`中的`FIELD`是`backing field`，java反射才能拿到，`PROPERTY`才是属性