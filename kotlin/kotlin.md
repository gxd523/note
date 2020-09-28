[TOC]

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
* `object : 接口名`：匿名对象，可以多实现，交叉类型
* `companion object`：伴生对象，类中的单例实例对象

#### 伴生
* 类内部定义的单例对象，可直接类名点调用对象的成员
* 用来替代静态工具类

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

### 代理
* <对象a>代理<类/属性>实现<接口/getter/setter>
* 接口代理：`class ApiWrapper(private val api: ApiImpl) : Api by api {`
* 属性代理`getter`：`operator fun getValue(thisRef: Any?, property: KProperty<*>): T {`
* 属性代理`setter`：`operator fun setValue(thisRef: Any?, property: KProperty<*>, value: T) {`
* `property`指`by`左边的委托对象，`property==::name`，`thisRef`为`property`作为属性对应类的实例
* 属性引用：`::name`，类型为`KProperty`
* 常用代理：`lazy`、`observable`

### 属性
#### backing field
* 扩展属性、接口属性、内联属性都没有

### 属性的延迟初始化
* 属性无论是`val`还是`var`都要初始化
* 可以初始化可空类型为null，之后再赋值
* `lateinit var`：延迟初始化，不支持基础类型，基础类型应用字面量初始化
* `::field.isInitialized`：判断是否已初始化
* 推荐使用`val imageView by lazy{}`来延迟初始化，会在imageView第一次被使用`lazy`的返回值初始化属性

## 泛型
* 为了兼容1.5之前的Java，所以才用伪泛型
* `泛型形参`：泛型声明，`泛型实参`：泛型实例化
* 泛型约束：`T : Comparable<T>`，多个约束：函数返回值之后加`where T : A, T : ()->T`
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
* `try/catch`表达式
* `crossinline`
* `reified`：和`inline`配合使用
* `const val`等价于Java的`final`，是编译期常量，`val`是运行时常量，编译器在编译期无法确定它的值，本质是个变量，仍然可以通过反射修改值
* @JvmStatic
* @JvmField

### 区间
* 用`..`隔开的，两端为基础类型，数字、字符，String不行
* `(1..2)`就是一个`IntRange`实例

### 集合变化&序列
* flatMap { 0 until it }
* asSequence()将流变成懒汉式
* 集合添加元素可以使用`+=`，移除`-=`，set、get元素`list[index]`
* Map是这样`map[key]`，所以集合和数组是一回事
* `Pair`：键值对，`1 to "a"`、`Pair(1, "a")`、`(x, y)`(解构表达式)，实际是个`data class`
* `Triple`：
* 判断包含关系：`元素 in 集合`