### 为什么重写了equals()后一定要重写hashCode()
* Hash类集合判断是否重复的条件是：hashCode相同 && ( 地址相同 || equals为true)
* equals默认比较对象的内存地址，hasCode默认返回散列表中的位置

### 为了Hash类集合重复判断，equals()&hashCode()要遵循如下规则
* 若两个对象equals()为true，则hashCode()相同
* 若两个对象hashCode()相同，则equals()不一定为true
* 若两个对象hashCode()不同，则equals()一定为false
* 若两个对象equals()为false，则hashCode()不一定不同
* 两个对象equals()为true是hashCode()相同的充分不必要条件
* 充分的一定能得出结论，必要的是结论的一部分