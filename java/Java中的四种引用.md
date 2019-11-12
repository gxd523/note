#### 强引用
> Java虚拟机宁愿抛出OutOfMemoryError错误，使程序异常终止，也不会回收强引用对象。

#### 软引用
> 只有内存不足时，才会回收软引用对象。

#### 弱引用
> 垃圾回收器工作时，就会回收弱引用对象。

#### 虚引用
> 等于没有引用。

```java
String str = new String("hello");
ReferenceQueue<String> referenceQueue = new ReferenceQueue<>();
PhantomReference<String> phantomReference = new PhantomReference<>(str, referenceQueue);
System.out.println(phantomReference.get());
str = null;
System.gc();// 不一定真正执行内存回收
System.runFinalization();// 貌似是强制执行内存回收，所以不加这句的话，referenceQueue.poll() == phantomReference的返回值不确定
System.out.println(referenceQueue.poll() == phantomReference);
```

#### ReferenceQueue
> 软引用、弱引用、虚引用可以和一个引用队列（ReferenceQueue）联合使用，如果软引用、弱引用、虚引用所引用的对象被垃圾回收，Java虚拟机就会把这个软引用加入到与之关联的引用队列中。

```java
String str = new String("hello");
ReferenceQueue<String> referenceQueue = new ReferenceQueue<>();
WeakReference<String> weakReference = new WeakReference<>(str, referenceQueue);
System.out.println(weakReference.hashCode());
str = null;

System.gc();

System.out.println(weakReference.get());

Reference<? extends String> reference = referenceQueue.poll();
System.out.println(reference == null ? "null" : reference.hashCode());
```