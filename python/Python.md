> Python是解释型动态强类型语言
### Python缺点
* Python是解释型语言(C是编译型语言)，代码在执行时会一行一行地翻译成CPU能理解的机器码，所以效率低。
* 由于解释型语言运行时需要翻译源码，所以Python代码不能加密。

### Python安装
cmd输入：`brew install python3`
**验证时记得加3输入`python3` **

### Python解释器
官方版本的解释器：CPython

### Python基础
* Python是**动态语言**，变量类型不固定；与之对应的是**静态语言**，例如Java
* Python的基础类型有整数、浮点数、字符串、布尔值
* 空值为None
* and等价&&，or等价||，not等价！
* Python没有常量，只是习惯性将大写变量视为常量

#### 字符串与编码
* 由于计算机是美国人发明的，因此，最早只有127个字符被编码到计算机里，也就是大小写英文字母、数字和一些符号，这个编码表被称为ASCII编码，比如大写字母A的编码是65，小写字母z的编码是122。
* ASCII编码是1个字节，而Unicode编码通常是2个字节(如果要用到非常偏僻的字符，就需要4个字节)
* 本着节约的精神，又出现了把Unicode编码转化为“可变长编码”的`UTF-8`编码，UTF-8编码把一个Unicode字符根据不同的数字大小编码成1-6个字节，常用的英文字母被编码成1个字节，汉字通常是3个字节，只有很生僻的字符才会被编码成4-6个字节
* UTF-8编码有一个额外的好处，就是ASCII编码实际上可以被看成是UTF-8编码的一部分，所以，大量只支持ASCII编码的历史遗留软件可以在UTF-8编码下继续工作
* Python提供了**ord()**函数获取字符的整数表示，**chr()**函数把编码转换为对应的字符
* **encode()**：'中文'.encode('utf-8')
* **decode()**：b'\xe4\xb8\xad\xe6\x96\x87'.decode('utf-8')
* **len()**：len('ABC')

#### 格式化
```python
'Hi, %s, you have $%d.' % ('Michael', 1000000)
```
占位符 | 替换内容
--- | ---
%d | 整数
%f | 浮点数
%s | 字符串
%x | 十六进制整数

### list与tuple
```
classmates = ['Michael', 'Bob', 'Tracy']
```
* 最后一个元素索引-1，以此类推
* 删除元素使用pop(index)方法
* 修改元素classmates[0] = 'aaa'
* tuple和list非常类似，但是tuple一旦初始化就不能修改
```
classmates = ('Michael', 'Bob', 'Tracy')
```
* list和tuple是Python内置的有序集合，一个可变，一个不可变。
* string转int：int('22')

### 循环

for

```python
names = ['Michael', 'Bob', 'Tracy']
for name in names:
    print(name)
```

while

```
sum = 0
n = 99
while n > 0:
    sum = sum + n
    n = n - 2
print(sum)
```

### dict与set
* dict类似map，键值对存储
* dict查找和插入的速度极快，不会随着key的增加而变慢，但需要占用大量的内存，内存浪费多，list相反
* 请务必注意，dict内部存放的顺序和key放入的顺序是没有关系的。
* 需要牢记的第一条就是dict的key必须是不可变对象
* 对于不变对象来说，调用对象自身的任意方法，也不会改变该对象自身的内容。相反，这些方法会创建新的对象并返回，这样，就保证了不可变对象本身永远是不可变的
* set元素不重复，无序
