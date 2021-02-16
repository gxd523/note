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