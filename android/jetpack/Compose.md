[TOC]

## 声明式UI
* `Compose`抛弃了`View`、`ViewGroup`，使用底层`Canvas`那一套东西，它的`渲染机制`、`布局机制`、`触摸算法`等都已完全不同
* `声明式UI`不同于`命令式UI`，不需要手动更新UI，当数据发生改变时会自动更新UI
* `Data Binding`也可以通过订阅的方式自动更新UI，但不能称为`声明式UI`，因为`Data Binding`只能更新数据，不能更新UI结构、布局等
* `Compose`更加灵活，性能更好
* `Swift UI`、`Flutter`都已是`声明式UI`
* 没有返回值，因为不需要手动更新

## 独立于平台
* Compose不在Android系统中，发布新版不依赖于Android系统的更新
* 不依赖Android原生控件，使IDE预览功能不再需要模拟Android系统解析、显示控件的过程，以更低的成本实现更加强大的预览功能(交互、动画)
* 可以像Flutter一样跨平台

## 6大group

名称 | 描述
:---: | :---
material | Material设计规范：Button()，material-icons-extended 
foundation | 基础控件：Column()、Row()、Image()、Text() 
animation | 动画 
ui | 测量、布局、绘制、触摸反馈，Layout()，ui-tooling：预览工具，单独引用 
runtime | 底层概念模型(数据结构、状态转换机制)，mutableStateOf()、remember()
compiler | kotlinCompilerExtensionVersion，负责编译过程，不使用API

## 控件
Android | Compose
:---: | :---:
TextView | Text
ImageView | Image
Button | Button
FrameLayout | Box
LinearLayout | Column/Row
RecyclerView ｜ lazyColumn

## 布局层级
```
decorView：FrameLayout
	LinearLayout
		android.R.id.content
			ComposeView
				AndroidComposeView
					root：LayoutNode
						Column：LayoutNode
							Text：LayoutNode
							Image：LayoutNode
```

## 解析过程
`Text()`-->`SlotTable`(为了提高更新效率，平面结构)-->`LayoutNode`(实际显示，树结构)

## Composable
* 为了识别出哪些方法要加参数(Composer,groupId)
* Composable函数只能在Composable函数内调用
* 实现xml布局+代码逻辑，可以根据逻辑决定布局内容，融合了xml布局的直观和自定义View可以写逻辑的强大
* Composable函数跟Activity的关联没那么紧密，可以到处复用
* `comp`快捷键，且函数名首字母大写

### Composable函数状态
* `Composable函数`是无状态的

### 调用栈
* AbstractComposeView.onAttachedToWindow()
* AbstractComposeView.ensureCompositionCreated()
* ViewGroup.setContent()
* doSetContent()
* WrappedComposition.setContent()
* CompositionImpl.setContent()
* ReComposer.composeInitial()
* CompositionImpl.composeContent()
* CompositionImpl.doCompose()
* invokeComposable()
* composable as Function2<Composer, Int, T>

## 状态订阅&自动更新
### mutableStateOf函数
* `value`的`getter`、`setter`被调用时，分别进行订阅、通知行为
* Compose的编译器插件会修改代码，把可能会被重新调用的代码块用函数包起来，并存储这些函数，当`value`被赋值时，找到并调用这些函数
* `mutableStateListOf()`
* `mutableStateMapOf()`
```kotlin
wrapperFunction {
	
}
```

### remember函数
* `remember{}`：第一次调用执行函数对象，并将返回值缓存到`SlatTable`中，之后调用时直接返回缓存的返回值
* `remember{}`用在可能会进入`Recompose`过程的变量上，也就是`Composable函数`中的`mutableStateOf`对象都要套`remember{}`

### 被动Recompose时的优化
* 因为Compose是声明式UI，不需要手动更新UI，会自动更新UI，但需要避免过度更新
* 在被动`Recompose`的时候，会自动判断`Composable函数`参数是否发生变化，没变化或没参数，就不调用函数内部代码了
* 如果`Composable函数`的参数为引用类型，且`equals()`内使用的属性都为`val`的，则以`equals()`判断参数是否变化
* 数据类如果加上`@Stable`，就表示数据类是可靠的，`equals()`不会变，不会被动刷新

名称 | 含义 | Kotlin | Java
:---: | :---: | :---: | :---:
Structral Equality | 内容相等 | ==/equals() | equals()
Referential Equality | 引用地址相等 | === | ==

## Modifier
> Modifer的函数调用顺序是有区别的

Android | Modifier
:---: | :---:
padding | padding在background的后面
margin | padding在background的前面
wrap_content | 默认
width/height | size
match_parent | fillMaxSize
onClickListener | clickable
textSize | fontSize函数参数 
textColor | color函数参数 

方法 | 含义
:---: | :---
clip() | 切边

为了能更原生控件交互，所以没有像Flutter一样采用NDK的方式去调用更底层的渲染引擎，而是使用Canvas去绘制

