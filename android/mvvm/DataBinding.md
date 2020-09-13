## 使用
* 第一步：gradle文件添加：
```
dataBinding {
    enabled = true
}
```
* 第二步：布局转换成`<layout>`包裹
* 第三部：`DataBindingUtil.setContentView()`替换`Activity.setContentView()`