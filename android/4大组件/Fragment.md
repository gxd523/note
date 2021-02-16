[TOC]

## 生命周期
![](https://gitee.com/hysbtr/pic/raw/master/fragment_lifecycle.png)

## FragmentTransaction(事务)
* 每`beginTransaction()`一次就会创建一个新的`FragmentTransaction`对象
* 使用`FragmentTransaction`对象进行一系列操作，`add`、`hide`、`replace`、`remove`等等
* 操作完成后，将整个事务`FragmentTransaction`提交，`commit`方式有4种
* 每个`FragmentTransaction`只能提交`commit`一次

### addToBackStack()
* 依次根据用户点击展示多个`Fragment`，按返回键时，依次退回到上个`Fragment`

### commit(提交)
* Activity执行完`onSaveInstanceState()`后不能再执行`commit()`

#### commit()
* 提交事务，提交后`Fragment`不会立即创建，而是由主线程异步来创建
* 也就是说使用`commit()`之后，`Fragment`不会被立即加入到`Activity`中
* 必须在`Activity`的`onSaveInstanceState()`调用之前提交，否则会抛异常

#### commitNow()
* 提交事务并立即执行，所有添加的`Fragment`会被立即创建，并开始生命周期，所有被移除的`Fragment`将会被立即移除
* `commitNow()`等价于`commit()`+`executePendingTransactions()`
* 必须在`Activity`的`onSaveInstanceState()`调用之前提交，否则会抛异常

#### commitAllowingStateLoss()
* 和`commit()`类似
* 不同之处：如果在`Activity`的`onSaveInstanceState()`调用之后提交，那么本次提交记录在`Activity`恢复的时候，可能不被保存

#### commitNowAllowingStateLoss()
* 和`commitNow()`类似
* 不同之处：如果在`Activity`的`onSaveInstanceState()`调用之后提交，那么本次提交记录在`Activity`恢复的时候，可能不被保存

## 其他
https://www.jianshu.com/p/a4c51309bc19

[者文_](https://www.jianshu.com/u/96099b5af4f1)

