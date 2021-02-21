a | byte | short | int | long | float | double | char | boolean | 引用 | 空对象
:---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---:
占用字节 | 1 | 2 | 4 | 8 | 4 | 8 | 2 | 1 | 4 | 8

## 创建对象的5中方式
* new
* 反射newInstance()
* 序列化
* clone
* `Unsafe.allocateInstance()`
```kotlin
val unsafeClass = Class.forName("sun.misc.Unsafe")
val f = unsafeClass.getDeclaredField("theUnsafe")
f.isAccessible = true
val unsafe = f.get(null)
val allocateInstance = unsafeClass.getMethod("allocateInstance", Class::class.java)
val person = allocateInstance.invoke(unsafe, Person::class.java)
```

## 位运算符
> 注意：负数要先转成补码

符号 | 名称 | 说明
:---: | :---: | ---
& | 按位与 | 全1为1，否则为0
\| | 按位或 | 有1为1，否则为0
~ | 按位非 | 单目运算符，将操作数的每个位（包括符号位）全部取反。
^ | 按位异或 | 相异为1，相同为0
<< | 左移运算符 | 空位补0
\>> | 右移运算符 | 空位补符号位的值，负1，正0
\>>> | 无符号右移运算符 | 空位补0

## 原码、反码、补码
正数的反码、补码等于原码
### 原码
数字的二进制表示
### 反码
除符号位外，每位取反
### 补码
反码+1

## Byte的范围为什么是-128~127
* Byte共8位，第1位表示符号，剩下7位表示大小
* 2^7位128，可以表示0~127
* -0和0都是0，为了不浪费-0，多表示一个数字(即-128)，发明了`补码`
* 负数使用`补码`表示
* 负数=正数取反再+1
* 正数=负数-1再取反
* 0的原码、补码相同
* -128的原码、补码相同

