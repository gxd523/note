[TOC]
## Kotlin是什么？
Kotlin是一门可以运行在**Java虚拟机**、**Android**、**浏览器**上的**全栈编程语言**(静态语言)

## Kotlin的特性

* Android官方开发语言
* 100%兼容Java
* Kotlin-Js前端开发
* Kotlin-Jvm服务端开发
* **空安全似懂非懂师傅的师傅的师傅**

* **Lambda表达式(匿名函数)**
* 函数式编程
* 运算符重载

## Kotlin相关网站
[Kotlin官网](https://kotlinlang.org)
[Kotlin文档](https://kotlinlang.org/docs/reference/)
[Kotlin源码](https://github.com/JetBrains/kotlin/)
[Kotlin博客](https://blog.jetbrains.com/kotlin/)

# 词法
## 基本类型
### Boolean

> val/var 变量名 : 类型 = 值

```kotlin
val isMember: Boolean = true
```
### Number

类型 | 位宽
---|---
Double | 64
Float | 32
Long | 64
Int | 32
Short | 16
Byte | 8

```kotlin
val age: Int = 0xff
val grade: Int = 0b11
val maxIngeter: Int = Int.MAX_VALUE
val minInteger: Int = Int.MIN_VALUE
val aLong: Long = 1231
val aFloat: Float = 2.0f
val aDouble: Double = 2.0
val maxByte: Byte = 127
val minByte: Byte = -128

fun main(args: Array<String>) {
    println(Math.pow(2.0, 31.0) - 1)
    println(0f / 0f)
    print(0f / 0f == Float.NaN)
}
```
### Char

character占16位，表示一个Unicode字符

```kotlin
val aChar: Char = '中'
val bChar: Char = '\u000f'
```

转义字符 | 含义
---|---
\t | tab
\b | 光标后退一个字符
\n | 回车
\r | 退格
\' | 单引号
\" | 双引号
\\ | 反斜杠
\$ | Kotlin支持$开头的字符串模板

### String
```kotlin
val aString: String = "Hello"
val fromChars: String = String(charArrayOf('H', 'e', 'l', 'l', 'o'))

fun main(args: Array<String>) {
    println(aString == fromChars)// ==与equals()是等价的
    println(aString === fromChars)// 比较对象的引用值，相当与Java的==
}
```
* 字符串模板

```kotlin
val arg1: Int = 1
val arg2: Int = 2
val rawString: String =
        """
            \r
            \t
            $
        """


fun main(args: Array<String>) {
    println("$arg1 + $arg2 = ${arg1 + arg2}")
}
```

### 类和对象

```kotlin
open class Person constructor(name: String, character: String, appearance: String) {
    init {// 相当于构造函数的函数体
        println("姓名$name,性格$character,相貌$appearance")
    }
}

class Girl constructor(name: String, character: String, appearance: String) : Person(name, character, appearance)

val myGril: Girl = Girl("小红", "内向", "一般")

fun main(args: Array<String>) {
    println(myGril is Person)
}
```

## 空安全

> 问号前面可能是null，如果是null，则赋值null，并执行冒号后面的

```kotlin
fun main(args: Array<String>) {
    var nullName = getNullName()
    // 如果为null则打印null
    println(nullName?.length)
    // 我确认这不是null
    println(nullName!!.length)
    // 如果为null则返回
    nullName = getNullName() ?: return
    println(nullName.length)
}

fun getName(): String {
    return "Jack"
}

fun getNullName(): String? {
    return null
}
```

## 智能类型转换

```kotlin
fun main(args: Array<String>) {
    val parent: Parent = Child()
    if (parent is Child) {// 内部不需要强转
        println(parent.getName())
    }

    val text: String? = "Hello"
    if (text is String) {// 表示text不为null且为String类型
        println(text.length)
    }

    val parent1 = Parent()
    // as就是cast,加？表示强转失败赋值null
    val child: Child? = parent1 as? Child
    println(child?.getName())
}

open class Parent

class Child : Parent() {
    fun getName(): String {
        return "name"
    }
}
```

## 包

```kotlin
import com.gxd.demo.Person as XPerson

val person : XPerson = XPerson()
```

## 区间(Range)

```kotlin
fun main(args: Array<String>) {
    // [0,1024]
    val range1: IntRange = 0..1024
    // [0,1024)
    val range2: IntRange = 0 until 1024
    println(range1.isEmpty())
    println(50 in range1)
    println(range1.contains(50))

    for (i in range1) {
        print("$i,")
    }

    val emptyRange: IntRange = 0..-1
    println(emptyRange.isEmpty())
}
```

## 数组

```kotlin
fun main(args: Array<String>) {
    // kotlin为基本类型定制了一套Array，避免不必要的拆箱、装箱
    val intArray: IntArray = intArrayOf(1, 3, 5, 7)
    val stringArray: Array<String> = arrayOf("aa", "bb", "cc")
    val objectArray: Array<Person> = arrayOf(Person("Tom", 12), Person("Marry", 17))

    println(intArray.size)
    for (i in intArray) {
        print("$i, ")
    }

    println(stringArray.joinToString("|"))
    println(intArray.slice(1..2))
}
```

# 语法
## val&var

> 和Java一样，写在类里的是成员变量，写在方法里的是局部变量，加val的是常量，加var的是变量

```kotlin
// 编译期常量,编译器在编译的时候就已经知道它的值了,并且把代码中其他地方对它的引用都替换成它的值,这样可以提高代码的运行效率,等同于Java的final
const val CONST_FINAL_STRING = "const_final_string"
// 运行时常量,编译器在编译的时候并不能确切的知道它的值到底是什么
val FINAL_STRING = "final_string"
val FINAL_INT = 4
// +号表示连接
var x = "Hello World!" + 5

fun main(args: Array<String>) {
    println(x)
}
```

## 函数

> 函数也是一种变量，可以作为参数，函数作为变量的类型由函数参数的个数、是否有返回值决定(例：Function0、Action0)

```kotlin
fun main(args: Array<String>) {
    println("hello ${args[0]}")

    println(result(1, 2))

    println(sum(1, 2))
}

fun sum(arg1: Int, arg2: Int) = arg1 + arg2

val result = fun(arg1: Int, arg2: Int) = arg1 + arg2

val result = fun(arg1: Int, arg2: Int) {
    return a + b
}
```

## Lambda表达式

> lambda表达式就是匿名函数，{形参->函数体}，没有形参可以省略->，函数体最后一行是函数的返回值

```kotlin
fun main(args: Array<String>) {

    println(sum.invoke(1, 2))

    println(printHello)

    println(sumAndPrint(1, 2))

    args.forEach {
        println(it)
    }

    // 函数体当做变量
    val function: (Int) -> Unit = { it -> println(it) }
    singleArg(function)

    // 一个参数可以不写->
    singleArg { println(it) }

    // 对于函数来说，如果它的最后一个参数是lambda表达式，可以将最后一个参数移到括号的外边
    singleArg() { println(it) }

    // 如果函数a体是一个函数b，且函数b需要的参数和函数a传入的参数相同，则可以进一步简化成以下形式
    singleArg(::println)
}

/**
 * 函数作为形参
 */
val singleArg = { funArg: (Int) -> Unit ->
    funArg(111)
}

/**
 * 函数体多行时，返回值为最后一行
 */
val sumAndPrint = { arg1: Int, arg2: Int ->
    println("$arg1 + $arg2 = ${arg1 + arg2}")
    arg1 + arg2
}

/**
 * 没有参数，所以不用加箭头
 */
val printHello: () -> Unit = { println("Hello") }

/**
 * 参数、返回值用箭头分隔
 */
val sum: (Int, Int) -> Int = { arg1: Int, arg2: Int -> arg1 + arg2 }
```

特殊用法:

```kotlin
fun main(args: Array<String>) {
    // 添加ForEach@，表示给大括号起名叫ForEach，使得return只跳出forEach而不是main
    args.forEach ForEach@{
        if ("q" == it) {
            return@ForEach
        }
        println(it)
    }

    println("The End")
}
```

## 类成员

>  方法是定义在类当中的，调用者是对象或者类本身；函数是一个功能，接收参数，返回结果。在Java当中只说方法不说函数，因为Java无法定义出严格意义的函数罢了(不是真正意义上支持函数式编程)。

```kotlin
class A {
    var b = 0
        get() {
            println("get value $field")
            return field + 1
        }
        set(value) {
            println("set value $value")
            field = value + 1
        }

    lateinit var c: String

    val d: String by lazy {
        println("lazy")
        "lazy d"
    }
}

fun main(args: Array<String>) {
    println("before")
    val a = A()
    println("start")
    a.b = 2
    println(a.b)
    println(a.d)
}
```

> 属性的初始化尽量在类的构造函数(init)中完成，如果无法再构造函数中初始化，尝试降级成局部变量，如果无法降级，var尝试lateinit延迟初始化，val使用lazy

## 运算符重载

> 任何类都可以定义或者重载父类的基本运算符，通过运算符对应的具名函数来定义，只对参数个数有要求，对参数类型、返回值无要求

```kotlin
class Plus {
    operator fun plus(arg1: Int): Int {
        println("hello plus")
        return arg1 * arg1
    }

    infix fun on(arg1: Int): String {// 一般不这么写，会降低可读性，dsl中会用到
        return "hello on $arg1"
    }
}

fun main(args: Array<String>) {
    val plus = Plus()
    println(plus + 2)
    println(plus on 3)
}
```

## 表达式

* 中缀表达式

> 只有一个参数，且用infix修饰的函数

* 分支表达式

> if是有返回值的，返回值为花括号里的最后一行

```kotlin
val USERNAME = "gxd"
val PASSWORD = "123"

fun main(args: Array<String>) {
    println("请输入用户名：")
    val username = readLine()
    println("请输入密码：")
    val password = readLine()

    // if是有返回值的，返回值为花括号里的最后一行
    val loginResult = if (username == USERNAME && password == PASSWORD) {
        "登陆成功！"
    } else {
        "登录失败！"
    }

    println(loginResult)
}
```

## when表达式

> 加强版的switch，支持所有类型，也有返回值

```kotlin
fun main(args: Array<String>) {
    val x = readLine()?.toInt()
    
    val txt = when (x) {
        1 -> "Terminal input 1"
        2 -> "Terminal input 2"
        else -> "nothing"
    }

    println(txt)
}
```

## 循环语句

```kotlin
fun main(args: Array<String>) {
    for ((index, value) in args.withIndex()) {
        println("$index -> $value")
    }
}
```

## 异常捕获

> try/catch也是表达式

```kotlin
fun main(args: Array<String>) {
    val result = try {
        val arg = args[2]
        println(arg)
    } catch (e: Exception) {
        e.message
    }
    println(result)
}
```

## 参数

* 具名参数

> 给函数传入实参的时候，把形参也附上

```kotlin
fun main(args: Array<String>) {
    val sum1 = sum(arg1 = 2, arg2 = 3)
    println(sum1 )
}
```

* 变长参数

```kotlin
fun main(args: Array<String>) {
    val array = intArrayOf(1, 3, 5, 7)
    hello(*array, string = "hello")
}

fun hello(vararg ints: Int, string: String) {
    //  equals  ints.forEach { println(it) }
    ints.forEach(::println)
    println(string)
}
```

* 默认参数

```kotlin
 fun main(args: Array<String>) {
    val array = intArrayOf(1, 3, 5, 7)
    hello(string = "hello", intArray = *array)
    hello(intArray = *array)
}

fun hello(string: String = "default string", vararg intArray: Int) {
    //  equals  ints.forEach { println(it) }
    intArray.forEach(::println)
    println(string)
}
```

# 面向对象
## 抽象类与接口

> 接口就是一种协议，抽象类是类的半成品

## 继承

> 一个类的父类决定他是什么，而他实现的接口决定他能做什么

```kotlin
open class Person(open val age: Int) {
    open fun printAge() {
        println(age)
    }
}

class Programmer(override val age: Int) : Person(age) {
    override fun printAge() {
        println("我的年龄是$age")
    }
}

class Animal(color: String) {
    fun printColor() {
        println("此时color不适成员变量，加上val、var就是了")
    }
}

/**
 * by关键字表示代理，此处为借口代理，还有一种属性代理
 */
class Killer(driver: Driver) : Driver by driver

class Worker : Driver

interface Driver {
    fun drive() {
        println("${this.javaClass.simpleName} is driving!")
    }
}

fun main(args: Array<String>) {
    val programmer = Programmer(27)
    programmer.printAge()

    val worker = Worker()
    val killer = Killer(worker)
    killer.drive()
}
```

