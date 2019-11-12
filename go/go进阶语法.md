[TOC]

### 函数
* 函数形式：`关键字 函数名(参数) 返回值`，例如`func xxx(a int) int {`
* 可以有多个返回值
* 返回类型加上变量名，使用时方便自动生成变量名
```go
func multiReturn() (a int, b int, c string) {
	a = 1
	b = 2
	c = "abc"
	return a, b, c
}
```
* 多个返回值如果有不用的，用`_`来接收这个值
* 函数可以作为参数传递
* 闭包：函数体内可以连接到外部变量，类似内部类持有外部的引用
* 函数也能实现接口...
* 函数是变量
* 匿名函数
```go
func() {
	fmt.Println("aaa")
}()
```

### 面向对象
* go语言不支持继承、多态，只有封装
* go语言没有构造函数
* go语言不需要关心变量在堆还是栈中
```go
var root treeNode
root = treeNode{value: 3}
root.left = &treeNode{}
root.right = &treeNode{5, nil, nil}
root.right.left = new(treeNode)
```
* 结构体定义方法：在`func`和函数名之间加`接收者`，其实和形参一样，只是调用时变成了：node.print()
```go
// node相当于this
func (node treeNode) print() {
	fmt.Println(node.value)
}
```
* nil指针也可以调用方法(内建类型、结构体都有默认初始化，指针默认为nil)
* go语言通过组合、定义别名，两种方式替代继承

### 接口
* go语言的接口更加灵活、强大，能够代替其他语言的继承功能
* `duck typing`：接口由使用者定义，而不是实现者(大黄鸭是不是鸭子又不同的人决定，小朋友认为是鸭子，生物学家认为不是鸭子)
* go语言属于结构化类型系统，类似`duck typing`
* go语言接口实现是隐式实现，不需要写类似java中的`implement Runnable`
* go语言的接口是由调用者定义的
* 接口由使用者定义，实现者只管实现功能，使用者根据自身需求定义接口，这种多态更加灵活
* 接口的实现类型可以是类，也可以是指针
* 接口对象中包含：`实现类型`、`接口实现实例地址`
* 接口变量同样是值传递，但由于内部包含指针，所以几乎不需要定义接口指针
* 查看接口类型(类似强转)：`Type Assertion`、`Type Switch`
* `interface{}`：表示任何类型
* `type assertion`：表示强转，例如`a.(int)`
  ```go
  name := "123"

  var b interface{}
  b = name

  if s, ok := b.(string); ok {
    fmt.Println(s)
  }

  switch b.(type) {
  case string:
    fmt.Printf("string->%s\n", b)
  case int:
    fmt.Printf("int->%d\n", b)
  }
  ```
* 接口可以组合
```go
type GetAndPost interface {
	Retriever
	Poster
	OtherMethod(url string) string
}
```

### Goroutine
* 协程是轻量级的线程：
	* 协同式线程调度，而非抢占式线程调度，由协程主动交出控制权
	* 编译器/解释器/虚拟机层面的多任务
	* go语言有自己的调度器
	* 多个协程可以在一个或多个线程上运行
	* 函数前面只需加上go就能给调度器运行
	* 在交出控制权的点上，go语言调度器可以进行优化，减少goroutine所要保存的状态的数量，减小开销
* goroutine可能的切换点：
	* I/O,select
	* channel
	* 等待锁
	* 函数调用(有时)
	* runtime.GoSched()

### Channel
> Don't communicate by sharing memory;sharing memory by communicating.
> 不要通过共享内存去通信，而是通过通信去共享内存。

* channel用于协程间通信
* channel的发送、接收都是阻塞式的(block)，直到发送的消息被接收、接收到发送的消息
* channel作为参数一般不用写成指针，内部包含指针
* channel默认值为`nil`
* `chan<- int`：发送数据类型，不能接收数据
* `chan-> int`：接收数据类型，不能发送数据
* `close(c)`：发送方关闭channel
* `make(chan int, 3)`：缓存3个channel
* 注意不要向未初始化的channel发送、接收数据
* 判断是否发完，搭配`close(c)`一起用，否则一直循环或ok不会返回false：
```go
for n := range inChannel {// 方法一
	fmt.Printf("%d----%c\n", id, n)
}

for {// 方法二
	n, ok := <-c
	if ok {
		fmt.Printf("%d...%d\n", id, n)
	} else {
		break
	}
}	
```
* Channel基于CSP模型(Communication Sequential Process)

### Select调度器
> `select`语句是一种仅能用于`channl`发送和接收消息的专用语句，此语句运行期间是阻塞的。

* `select`会判断每一个`case`能否成功发送或接收消息，当没有`case`满足条件时，阻塞当前`goroutine`，直到有`case`能够发送或接收消息。
* `case`中的channel如果为`nil`，则此`case`不能发送或接收消息，也不会抛异常。
* 多个`case`同时满足条件时，`select`会随机执行其中的一个
* 末尾加上`default`，当没有能够成功发送或接收的`case`时，则执行`default`，而不是阻塞当前`goroutine`

### 指针类型使用原则
* 如果`receiver`是较大的`struct`或者`array`，使用指针则更加高效。
* 如果`receiver`会被函数修改，则只能使用指针。
* slice、channel、map一般不用定义成指针，内部包含指针