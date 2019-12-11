[TOC]

#### 断点执行到指定行
> 在任意一段代码上，点击行号，可以从当前断点快速执行到目标行并暂停

#### 错误栈分析
> 选择 Analyze → Analyze Stack Trace or Thread Dump，然后把异常栈信息粘贴进去，点确定。

#### 挂起线程
> 断点点击右键，弹出对话框中，suspend选择`Thread`，表示之暂停当前线程，否则暂停所有线程

#### 计算表达式
> `Evaluate Expression`：缺点是断点执行到下一步时，需要手动点击计算

#### 观察对象
> `Mark Object`：debug相同的对象会以你设置的 Label 为 name 出现，帮助我们方便的分析是否是统一对象。

#### Log过滤折叠
> 选中需要折叠的log，鼠标右键，选择`Fold lines like this`

####  断点上一步
> 断点调试时选择`Drop Frame`，即可返回上一步