[TOC]

## SparseArray
* `SparseArray`是`key`为`int`类型的`Map`
* `SparseArray`的`key`、`value`分别是2个数组，避免了`key`的自动装箱
* 采用二分查找提高查找获取下标，效率更高
* 虽说SparseArray性能比较好，但是由于其添加、查找、删除数据都需要先进行一次二分查找，所以在数据量大的情况下性能并不明显，将降低至少50%
* 满足下面两个条件我们可以使用SparseArray代替HashMap：
	1. 数据量不大，最好在千级以内
	2. key必须为int类型，这中情况下的HashMap可以用SparseArray代替

## ArrayMap
- ArrayMap是一个<key,value>映射的数据结构，它设计上更多的是考虑内存的优化，内部是使用两个数组进行数据存储，一个数组记录key的hash值，另外一个数组记录Value值，它和SparseArray一样，也会对key使用二分法进行从小到大排序，在添加、删除、查找数据的时候都是先使用二分查找法得到相应的index，然后通过index来进行添加、查找、删除等操作，所以，应用场景和SparseArray的一样，如果在数据量比较大的情况下，那么它的性能将退化至少50%

- ArrayMap应用场景：
	1. 数据量不大，最好在千级以内
	2. 数据结构类型为Map类型


