#### 包装类型&基础类型
```java
Integer i1 = 3;
Integer i2 = new Integer(3);
Integer i3 = 3;
int i4 = 3;
System.out.println(i1 == i2); // false，new会重新在堆内存中创建
System.out.println(i1 == i3); // true，引用地址相同
System.out.println(i1 == i4); // true，i1会先拆箱成int，在和i3比较
```

```java
Integer f1 = 100;
Integer f2 = 100;
Integer f3 = 150;
Integer f4 = 150;
System.out.println(f1 == f2);   // true，当int在[-128,127]内时，结果会缓存起来
System.out.println(f3 == f4);   // false，属于两个对象
```

### Java如何跳出多层循环
* break+标签
```java
myLabel:
for (int i = 0; i < 10; i++) {
    for (int j = 0; j < 5; j++) {
        System.out.printf("i = %s, j = %s\n", i, j);
        if (i == 2 && j == 3) {
            break myLabel;
        }
    }
}
```

* 抛出RuntimeException
```java
try {
    for (int i = 0; i < 10; i++) {
        for (int j = 0; j < 5; j++) {
            System.out.printf("i = %s, j = %s\n", i, j);
            if (i == 2 && j == 3) {
                throw new RuntimeException();
            }
        }
    }
} catch (RuntimeException e) {
    e.printStackTrace();
}
```
* boolean标记
```java
boolean myLabel = true;
for (int i = 0; i < 10 && myLabel; i++) {
    for (int j = 0; j < 5 && myLabel; j++) {
        System.out.printf("i = %s, j = %s\n", i, j);
        if (i == 2 && j == 3) {
            myLabel = false;
        }
    }
}
```

#### 深浅拷贝
* 浅拷贝：将对象的成员变量直接复制值，引用类型也直接复制引用地址
* 深拷贝：将对象的基本类型成员变量直接复制值，引用类型则创建新的实例

#### 创建实例有几种方式
* new
* Class.newInstance()
* Constructor.newInstance()
* clone(不需要调构造函数)
* 反序列化(不需要调构造函数)