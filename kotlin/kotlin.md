[TOC]

## lamda
* 匿名函数，所以省去`fun`关键字
* 基本格式：`{`参数`->`函数体`}`
* 最后一行为返回值，所以不用写`return`
* 注意参数不用括号包裹，函数体不用花括号包裹
* 没有参数就没有`->`，一个参数默认为`it`，可以省略`it ->`
* `sum(1, 2)`等价于`sum.invoke(1, 2)`，函数作为变量调用时的`()`其实是运算符，`invoke()`是对应的具名函数
* `inline fun funcA(lamdaArg : ()->Unit)`函数(funA)带有`inline`修饰符时，`lamda`表达式里的`return `表示从`lamda`所在的函数(funA)返回，注意：`lamda`只是表达式，不算真正的函数，其他情况下`lamda`内貌似不能`return`，要加标签`return@Label`
* 函数类型的参数，可以传入`lamda`表达式，也可以传入符合参数和返回值定义的任意函数（此时叫作函数引用，例`::print`），此时用`::函数名`表示，如果是成员函数用`类名::函数名`表示
* 注意：将函数引用传入时(`::`方式)，如果函数引用是扩展函数，`类名::函数名`要多加一个类名的实例对象参数，`对象::函数名`则不用

## 高阶函数
* 函数作为参数或返回值的函数叫做`高阶函数`
* 最后一个参数为函数类型，可将`lamda`写到括号外，`abc(123){...}`，如果只有一个参数，可省略括号，`abc{...}`

### 常见高阶函数
函数 | 含义
:---: | :---
`let()` | T.let(block: (T) -> R): R
`use()` | T.use(block: (T) -> R): R	(T : Closeable?)
`run()` | T.run(block: T.() -> R): R 
`also()` | T.also(block: (T) -> Unit): T
`apply()` | T.apply(block: T.() -> Unit): T
`with()` | with(receiver: T, block: T.() -> R): R，和`run()`很想 
`forEach()` | 
`map()` | 元素映射成新的元素，再把元素组合成集合
`flatmap()` | 元素映射成集合，再把集合拼接成新的集合
`reduce()` | 将元素依次按规则聚合，元素类型不变
`fold()` | 给定初始值，将元素依次按规则聚合，元素类型变为初始值类型
`filter()` | 集合过滤元素 
`first()` | 第一个满足条件的
`take()` | 取前n个元素
`takeWhile()` | 取到不满足条件为止的n个元素
`takeIf()` | 满足条件就取
`block()` | 
`groupBy()` | 

## 扩展
* 扩展函数：带有接收者的函数，`<接收者类型>.<method>()`
* 扩展函数内会带有接收者(Receiver)的引用，可看做隐藏参数
* 扩展函数可替代Java中的静态工具类
* 扩展函数需要扩展类的对象调用
* 包级扩展函数，也可以在扩展类内部调用，基本和成员函数没差了
* 扩展属性由于没有`backing field`，所以要显示定义访问器

## 运算符重载
* 重载方法必须是扩展或成员函数
* 只能重载已有的运算符，例`+`、`-`、`*`、`/`
* 重载函数的名称必须为运算符对应的`具名函数`
* 对参数个数有要求，参数类型及返回值不做要求
* 基础类型的运算符禁止重载
* 一般写成类中的成员函数，控制重载范围

操作符 | 函数名
:---: | :---:
a+b | plus
a-b | minus
a*b | times
a/b | div
a%b | rem
a+=b | plusAssign
a-=b | minusAssign
a*=b | timesAssign
a/=b | divAssign
a%=b | remAssign
+a | unaryPlu
-a | unaryMin
!a | not
++a , a++ | inc
–a , a– | dec
== | equals()
!= | !equals()
\> , < , >= , <= | compareTo()
... | rangeTo()

## 闭包
* 在一个函数A中返回了另一个函数B,只有函数B才可以访问函数A中的内容,这个现象的概念叫做闭包
* 内部方法持有外部方法的状态(变量之类的)

## 内联
* 内联函数：`inline`修饰的高阶函数
* 内联函数实际是将函数体(函数类型的参数)内联到函数调用处，避免创建函数参数对象及闭包，减少了性能开销
* 内联函数，函数类型的参数前加上`crossinline`，表示`lamda`禁止`non-local return`
* 内联函数，函数类型的参数前加上`noinline`，表示不内联
* 内联属性不能有`backing field`，因为内联的是`getter`、`setter`函数
* 

## 伴生
* 类内部定义的单例对象，可直接类名点调用对象的成员
* 用来替代静态工具类

## 代理
* 代理具体指代理`getter`、`setter`
* `by`是运算符重载，对应具名函数`getValue(thisRef: Any?, property: KProperty<*>)`、`setValue(thisRef: Any?, property: KProperty<*>, value: T)`
* `property`指`by`左边的委托对象，`thisRef`为`property`作为属性所在的类的实例

## backing field
* 扩展属性、接口属性、内联属性都没有

## 中缀表达式
* `infix`修饰的，只有一个参数的，成员或扩展函数
* 避免滥用，会降低可读性
* 一般用在`DSL`

## 类
* 用主构造函数+默认参数替代副构造函数
* 可以定义看起来跟构造函数一样的工厂函数来扩展构造方式
* `internal`的访问范围是模块，jar包，aar，module都算是一个模块
* Java中的`default`可以通过外部创建相同包名访问
* kotlin的`internal`对Java无效，但可以`@JvmName(%aa)`，让Java无法访问
* `getter`，`setter`也可以加访问修饰符
* `顶级声明`：在文件中定义的属性、函数、类等

### object
* `object 类名`：单例类
* `object : 接口名`：匿名对象
* `companion object`：伴生对象，类中的单例实例对象

### 数据类
* 自动生成`toString()`、`hashCode()`
* 解构表达式：`val (id, name) = Country(1, "美国")`
* `(属性, 属性...)`是运算符，具名函数为`componentN()`
* 数据类型不可继承，可用`allopen`插件解决
* 数据类型没有无参构造函数，可用`noarg`插件解决

### 内部类
* `kotlin`内部类默认是静态的，非静态需要加`inner`
* 内部类调用外部类的成员用`this@外部类名`
* 匿名内部类用`object : 类或接口名`生成
* `kotlin`里的`object`匿名内部类更强大，可以匿名继承实现多个类或接口

### 密封类
* 密封类`sealed class`，子类需定义在相同文件中，个数可数，类似枚举实例可数

## DSL
* 编写`Gradle`的`Grovvy`是动态语言，编译时不做检查，所以IDE提示也很差

## 协程Coroutine
* 协程基础库：`org.jetbrains.kotlinx:kotlinx-coroutines-core:1.3.7`
* 协程Android库，提供Android UI调度器：`org.jetbrains.kotlinx:kotlinx-coroutines-android:1.3.4`
* 启动模式：
	* `DEFAULT`：立即开始调度协程体
	* `LAZY`：只有在需要(start/join/await)时才开始调度
	* `ATOMIC`：立即开始调度，且在第一个挂起点前不能被取消
	* `UNDISPATCHED`：立即在当前线程执行协程体，直到遇到第一个挂起点(后面取决于调度器)

### Job
* 可以对应Java的`Thread`来理解

### 调度器
* 继承`CoroutineContext`

### 作用域
### Context
* CoroutineContext


### 拦截器
### 挂起函数

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

## 泛型
* 为了兼容1.5之前的Java，所以才用伪泛型
* 使用`inline`与`reified`
* `inline fun <reified T> Gson.fromJson(json: String): T = fromJson(json, T::class.java)`
* `Nothing`：所有类型的子类
* 星投影`*`：表示不告诉类型，在协变中为`Any?`，在逆变中为`Nothing`
* 星投影只能用在声明的泛型中
* 不变=协变+逆变，协变管`get()`、逆变管`set()`

A| B | D | E | *含义 | 类比Java
:---: | :---: | :---: | :---: | :---: | :---:
out | 协变 | 上边界 | get() | Any? | extends
in | 逆变 | 下边界 | set() | Nothing(所有类的子类) | super

## Java&Kotlin混合开发
* `@JvmField`：

## 其他
* Java的静态方法及属性可用`包函数`、`扩展函数`、`伴生对象`替换
* 可见性修饰符`internal`范围为同一`module`下
* 延迟属性
* `try/catch`表达式
* 函数可变数量参数`vararg`
* `高阶函数`：作为形参或返回值或变量的函数，必须为匿名函数
* `具名或匿名`
* `crossinline`
* `reified`：和`inline`配合使用
* `const val`等价于Java的`final`，是编译期常量，`val`是运行时常量，编译器在编译期无法确定它的值，本质是个变量，仍然可以通过反射修改值

### 区间
* 用`..`隔开的，两端为基础类型，数字、字符，String不行
* `(1..2)`就是一个`IntRange`实例

### 尾递归
* `递归`能实现的`迭代`也能实现
* `递归`返回判断处理不当或层数太深，可能造成`stackoverflow`，`迭代`不会
* `迭代`效率高，但可读性差
* 递归函数加`tailrec`修饰符，会将递归转为迭代
* 尾递归：递归函数在调用自己之后没有其他代码

### 柯里化Curry&偏函数
* 多元函数变换成一元函数调用链
* `fun curryFoo(a: Int) = fun(b: Boolean) = fun(c: Float): String`
* 调用：`curryFoo(1)(true)(2.3f)`
* `fun <P1, P2, P3, R> Function3<P1, P2, P3, R>.convertCurry() = fun(p1: P1) = fun(p2: P2) = fun(p3: P3) = this(p1, p2, p3)`
* 偏函数：多元函数传入部分参数后得到的新函数
* `fun <P1, P2, R> Function2<P1, P2, R>.convertPartial(p2: P2) = fun(p1: P1) = this(p1, p2)`

### 集合变化&序列
* flatMap { 0 until it }
* asSequence()将流变成懒汉式
* 集合添加元素可以使用`+=`，移除`-=`，set、get元素`list[index]`
* Map是这样`map[key]`，所以集合和数组是一回事
* `Pair`：键值对，`1 to "a"`、`Pair(1, "a")`、`(x, y)`(解构表达式)，实际是个`data class`
* `Triple`：
* 判断包含关系：`元素 in 集合`

### SAM(Single Abstract Method)
* Java的`lamda`是假的，本质是SAM转换
* Java中的SAM转换：只有一个方法的接口，调用时可用`lamda`，最终会转换成匿名内部类
* Kotlin中SAM转换：只有一个方法的Java接口，调用时可用`lamda`(`Runnable {}`)，最终会自动转换为`object : Runnable {run(){ `lamda`中的代码}}`，相当于把`lamda`内联到单接口方法中
* Kotlin中的`lamda`与单接口不是一回事，因为Kotlin中的`lamda`是有类型的，例：`()->Unit`，所以Kotlin的单接口并不支持SAM转换
* `setListener(listner)`，传参时，要写`val listener = Listener{}`，然后可以`removeListener(listener)`，最推荐还是`val listener = object : Listener`，不管怎样都要防止SAM转换，因为会生成匿名内部对象
