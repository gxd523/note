[TOC]

## 生命周期
![](https://gitee.com/hysbtr/pic/raw/master/activity_lifecycle.png)

* A跳转B时的执行顺序：`A.onPause()`、`B.onCreate()`、`B.onStart()`、`B.onResume()`、`A.onStop()`、`A.onSaveInstanceState()`
* A跳转B，B返回时的执行顺序：`B.onPause()`、`A.onRestart()`、`A.onStart()`、`A.onResume()`、`B.onStop()`、`B.onDestroy()`
* 注意：B透明时，A跳转B，A不会执行`onStop()`，因为A仍然可见
* Activity上覆盖全屏的Dialog，不会让Activity生命周期发生变化，仍然是前台进程，因为Dialog是Activity内的一部分，所以即便完全遮盖Activity，显示的也是Activity里的Dialog，要被其他Activity遮挡才会导致声明周期变化

#### A跳转B生命周期执行顺序
* 

### 启动方式
#### 显示调用
#### 隐式调用
* action
* packageName + activityName
* uri

### 主要方法
#### onResume()
> 进入前台，可以响应交互操作

#### onRestart()
> 跳转其他Activity再返回、按Home键再打开、切换到其他应用在切回等执行了onStop()但未执行onDestroy()时，当Activity再次返回前台便会执行onRestart()

#### onSaveInstanceState()&onRestoreInstanceState()
* 当Activity发生旋转、屏幕尺寸改变、键盘隐藏等情况时会销毁并重新创建Activity。
* onSaveInstanceState()用于保存一些重新创建Activity时恢复之前状态的数据，会在onStop()之后调用。
* onRestoreInstanceState()用于获取之前onSaveInstanceState()保存的数据(也可在onCreate()中获取)，在onStart()之后调用。

#### onNewIntent()
> 在singleTop、singleTask、singleInstance模式中如果Activity已在任务栈，就会复用(只回调onPause()、onResume())，就会执行onNewIntent()