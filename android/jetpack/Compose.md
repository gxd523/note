[TOC]

## 声明式UI
* `Compose`抛弃了`View`、`ViewGroup`，使用底层`Canvas`那一套东西，它的`渲染机制`、`布局机制`、`触摸算法`等都已完全不同
* `声明式UI`不同于`命令式UI`，不需要手动更新UI，当数据发生改变时会自动更新UI
* `Data Binding`也可以通过订阅的方式自动更新UI，但不能称为`声明式UI`，因为`Data Binding`只能更新数据，不能更新UI结构、布局等
* `Compose`更加灵活，性能更好
* `Swift UI`、`Flutter`都已是`声明式UI`
