## 泛型
* 为了兼容1.5之前的Java，所以才用伪泛型
* 伪泛型：编译后泛型会被上限类型替代，默认为Any?
* 伪泛型的问题：编译后才用上限类型代替擦除的泛型，所以编译时仍没有类型
	* 无法创建实例：`val t = T()`
	* 无法获取类型：`val jclass = T::class.java`
* `泛型形参`：泛型声明，`泛型实参`：泛型实例化(具体类型)
* 泛型约束：`T : Comparable<T>`，多个约束：函数返回值之后加`where T : A, T : ()->T`
* 内联特化：使用`inline`与`reified`，在函数调用处织入代码，泛型被替换为具体类型
* `inline fun <reified T> Gson.fromJson(json: String): T = fromJson(json, T::class.java)`

### 型变&星投影
* 型变、星投影都用于类，不能用于函数

A| B | D | E | *含义 | 类比Java
:---: | :---: | :---: | :---: | :---: | :---:
out | 协变 | 上限 | get() | 取上限，默认为Any? | extends
in | 逆变 | 下限 | set() | 取下限，只能为Nothing | super

* 协变点：函数返回值类型为泛型参数，包括val、var的getter
* 逆变点：函数参数类型为泛型参数，包括var的setter
* `out A : Number`：表示上限为`Nummber`类型，下限则无法定义
* 强转可以加泛型，`as Function<Int, Any>`，实际是为了骗过编译器
* `is Function<Int, Any>`不可以加泛型，因为运行时泛型已经擦除了
* `Nothing`：所有类型的子类
* 星投影`*`：表示不告诉类型，在协变中为`Any?`，在逆变中为`Nothing`，对应Java的?
* 星投影只能用在泛型形参中
* `HashCode<String,List<*>>`，此时的value：`List<*>`，可以使用星投影
* 确定上界：\<T : TextView>，多个\<T> where T : TextView, T : Viewer
* `@UnsafeVariance`：协变的泛型想要作为函数的参数，或者逆变的泛型想要作为函数的返回值

### 实践
* `func(a:T,b:T)`，如果a、b类型不一样，以a、b的父类型作为T的实参
* `func(a:T,b:List<T>)`，如果a、b类型不一样，则以b的父类型作为T的实参，因为要先确定List的T的实参
* 以下情况：`func(B(), Out<C>())`：E的实参为B，`func(B(), Out<A>())`：E的实参为A
```kotlin
open class A
open class B : A()
open class C : B()
class Out<out T>

fun <E> func(a: E, b: Out<E>){}
```

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

#### 获取Kclass&Class
- | KClass | Class
:---: | :---: | :---:
对象 | a::class<br/>a.javaClass.kotlin | a::class.java<br/>a.javaClass 
类 | A::class | A::class.java