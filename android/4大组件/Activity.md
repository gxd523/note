[TOC]

#### A跳转B生命周期执行顺序
* `A.onPause()`-->`B.onCreate()`-->`B.onStart()`-->`B.onResume()`-->`A.onStop()`

### 启动方式
#### 显示调用
#### 隐式调用
* action
* packageName + activityName
* uri