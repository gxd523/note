[TOC]

## Java&Kotlin混合开发
* `@JvmField`：去除getter、setter，变成静态属性
* `@JvmStatic`：将`object`、`companion object`中函数变成Java中的静态函数
* `@JvmName`：
	* 作用在文件上：指定包级函数在java中的类名：@file:JvmName("TextUtil")
	* 作用在方法上：改变java调用时的方法名
	* 作用在属性上：改变java调用时的属性名
* `@JvmOverloads`：为参数含有默认值的函数、构造函数，生成对应的重载方法，因为Java中的函数、构造函数的参数不支持默认值

## Kotlin实现Java的static
* 顶层申明
* `object`、`companion object`(不是官方建议)
* 建议每个工具类创建一个kt文件用`顶层申明`的方式写工具方法
* 像TAG这种，属于类的变量，可以用`companion object`

## SAM(Single Abstract Method)
* Java没有函数类型，所以单函数接口即使用`lamda`表示：`Runnable runnable = () -> { };`表示，`lamda`的类型仍然是`Runnable`，所以`lamda`本质是匿名内部类
* 在Kotlin中，如果将函数类型的变量(如`lamda`)，赋值给单函数Java接口类型的参数，就会发生SAM转换，将函数类型替换成匿名内部类对象`object : Runnable{...}`
* 注意：Kotlin中`lamda`是函数类型，单函数接口是类类型，完全不等价
* 注意：`Runnable{}`是`Runnable`接口类型匿名对象，等价于`object : OnClickListener {...}`，而`{view:View->}`是`(View)->Unit`函数类型
* 所以将`{}`赋值给`Runnable`类型的参数时，会SAM转换成`object : Runnable {...}`，将`lamda`替换成生成的`Runnable`类型匿名对象

## Gson
* `data class`不能被继承：allOpen
* `data class`没有无参构造函数：noArg
* Gson找不到合适的构造函数，会调用`sun.misc.Unsafe`的`allocateInstance()`，不使用任何构造函数，直接创建对象
	* 可能引发空安全失效问题，所以`data class`最好加上默认值