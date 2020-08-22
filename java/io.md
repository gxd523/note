[TOC]

### 字节流
#### InputStream
#### OutputStream
### 字符流
#### Reader
#### Writer

### 常用类总结
#### 文件
* `FileInputStream`、`FileOutputStream`
* `FileReader`、`FileWriter`

#### 数组
* `ByteArrayInputStream`、`ByteArrayOutputStream`
* `CharArrayReader`、`CharArrayWriter`

#### 缓冲
* `BufferedInputStream`、`BufferedOutputStream`
* `BufferedReader`、`BufferedWriter`

#### 字节/字符互转
`ReaderInputStream`、`WriterInputStream`、`InputStreamReader`、`InputStreamWriter`

#### 对象序列化
`ObjectInputStream`、`ObjectOutputStream`、`ObjectReader`、`ObjectWriter`

#### 基本数据类型
`DataInputStream`、`DataOutputStream`

#### 管道
`PipedInputStream`、`PipedOutputStream`、`PipedReader`、`PipedWriter`

#### 打印
`PrintStream`、`PrintWriter`

#### 控制台
`System.in`、`System.out`

### try-with-resources
不用手动close()

### BufferedWriter对比PrintWriter
Socket编程中,尽量用PrintWriter取代BufferedWriter，下面是PrintWriter的优点：
1. PrintWriter的print、println方法可以接受任意类型的参数，而BufferedWriter的write方法只能接受字符、字符数组和字符串；
2. PrintWriter的println方法自动添加换行，BufferedWriter需要显示调用newLine方法；
3. PrintWriter的方法不会抛异常，若关心异常，需要调用checkError方法看是否有异常发生；
4. PrintWriter构造方法可指定参数，实现自动刷新缓存（autoflush）；
5. PrintWriter的构造方法更广。