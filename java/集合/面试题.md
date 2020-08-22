#### HashMap的数据结构
> 数组+链表/红黑树

#### HashMap的存储过程
* 对key的hashCode()进行hash计算后得到数组index
* 如果当前数组为null，则初始化容量为16的数组
* 如果hash没有碰撞则直接放入对应数组下标
* 如果hash碰撞，则放入链表末尾(>8需要转成红黑树)
* 完成 put 后，是否需要 resize () 操作扩展数组，数据量超过 threshold，threshold 为初始容量和负载因子之积，默认为12

#### hashCode()相同如何获取对象
> 这种情况说明，此hashCode()对应数组元素的链表或红黑树又多个元素，它们的hashCode()相同，通过比较equals()来获取

#### 解决hash冲突的办法
* 扩容数组
* 优化算法
* 链表
* 红黑树

#### HashMap&HashTable区别
* HashMap 是线程不安全的，HashTable 是线程安全的。
* HashMap 的键需要重新计算对象的 hash 值，而 HashTable 直接使用对象的 hashCode
* HashMap 的值和键都可以为 null，HashTable 的值和键都不能为 null。
* HashMap 的数组的默认初始化大小为 16，HashTable 为 11；HashMap 扩容时会扩大两倍，HashTable 扩大两倍 + 1；

#### ArrayList&LinkedList异同
* 相同点：有序、可重复、非线程安全
* 不同点：分别基于数组结构、双向链表结构