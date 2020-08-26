[TOC]

## 概述
![](https://github.com/part5/note/raw/master/pic/hash_map.png)

* `HashMap`是`数组+单链表/红黑树`的数据结构
* 数组元素超过一个会转成链表，>=8(并且数组长度大于64)个会转成红黑树，<=6个会转回链表
* `key`在数组中的位置`index`计算方式：`key.hash & table.length - 1`
* 数组默认长度是16，且只能是2的幂
* 当 `数组元素个数`>`数组容量`\*`loadFactor` 时，`将数组容量`\*2
* 数组扩容后，需要重新计算`key`在数组中的位置`index`，非常消耗性能
* `Java 8`中通过`key.hash & oldCap`的结果是否为1，判断扩容后是否需要移动位置，减少了扩容带来的性能消耗

## equals()&hashCode()
* `==`在基础类型中比较值是否相等，引用类型比较地址
* `equals()`默认比较地址是否相等
* `hashCode()`返回的不是对象的存储地址
* `equals()`&`hashCode()`要遵循如下规则
	* 若两个对象equals()为true，则hashCode()一定相同
	* 若两个对象hashCode()相同，则equals()不一定为true
	* 若两个对象hashCode()不同，则equals()一定为false
	* 若两个对象equals()为false，则hashCode()不一定不同

## 常见问题
### HashMap中数组的长度为什么是2的幂
因为计算index时，通过key.hashCode()&(table.length-1)，长度取2的幂减1后的二进制是连续的1，使key能够均匀分布在table数组中

### 数组扩容后为什么要rehash
其实是重新计算`key`在数组中的位置，因为位置计算公式为`key.hashCode & table.length - 1`，数组容量改变后之前key在新数组中的位置`index`也改变了

### 为什么重写了equals()后一定要重写hashCode()
重写了equals()则表示不再以内存地址判断两个对象是否相等，此时如果不重写hashCode()，会出现存入两个hashCode相同，但内存地址不同的key，如果此时两个key的equals返回true，则存入第二个时就会覆盖第一个key。

