https://www.jianshu.com/p/7790ca1bc8f6
###io包
###bufio包
* `type Reader struct {}`：首先定义了一个用来缓冲io.Reader对象的结构体，同时该结构体拥有以下相关的方法
* `func NewReader(rd io.Reader) *Reader`：NewReader函数用来返回一个默认大小buffer的Reader对象(默认大小好像是4096) 等同于NewReaderSize(rd,4096)
* `func NewReaderSize(rd io.Reader, size int) *Reader`：该函数返回一个指定大小buffer(size最小为16)的Reader对象，如果 io.Reader参数已经是一个足够大的Reader，它将返回该Reader
* `func (b *Reader) Buffered() int`：该方法返回从当前buffer中能被读到的字节数
* `func (b *Reader) Discard(n int) (discarded int, err error)`：Discard方法跳过后续的 n 个字节的数据，返回跳过的字节数。如果0 <= n <= b.Buffered(),该方法将不会从io.Reader中成功读取数据。
* `func (b *Reader) Peek(n int) ([]byte, error)`：Peekf方法返回缓存的一个切片，该切片只包含缓存中的前n个字节的数据
* `func (b *Reader) Read(p []byte) (n int, err error)`：把Reader缓存对象中的数据读入到[]byte类型的p中，并返回读取的字节数。读取成功，err将返回空值
* `func (b *Reader) ReadByte() (byte, error)`：返回单个字节，如果没有数据返回err
* `func (b *Reader) ReadSlice(delim byte) (line []byte, err error)`：该方法在b中读取delimz之前的所有数据，返回的切片是已读出的数据的引用，切片中的数据在下一次的读取操作之前是有效的。如果未找到delim，将返回查找结果并返回nil空值。因为缓存的数据可能被下一次的读写操作修改，因此一般使用ReadBytes或者ReadString，他们返回的都是数据拷贝
* `func (b *Reader) ReadBytes(delim byte) ([]byte, error)`：功能同ReadSlice，返回数据的拷贝
* `func (b *Reader) ReadString(delim byte) (string, error)`：功能同ReadBytes,返回字符串
* `func (b *Reader) ReadLine() (line []byte, isPrefix bool, err error)`：该方法是一个低水平的读取方式，一般建议使用ReadBytes('\n') 或 ReadString('\n')，或者使用一个 Scanner来代替。ReadLine 通过调用 ReadSlice 方法实现，返回的也是缓存的切片，用于读取一行数据，不包括行尾标记（\n 或 \r\n）
* `func (b *Reader) ReadRune() (r rune, size int, err error)`：读取单个UTF-8字符并返回一个rune和字节大小

###ioutil包
* `  func ReadAll(r io.Reader) ([]byte, error)`：从一个io.Reader类型中读取内容直到返回错误或者EOF时返回读取的数据，当err == nil时，数据成功读取到[]byte中。ReadAll函数被定义为从源中读取数据直到EOF，它是不会去从返回数据中去判断EOF来作为读取成功的依据
* `  func ReadDir(dirname string) ([]os.FileInfo, error)`：读取一个目录，并返回一个当前目录下的文件对象列表和错误信息
* `func ReadFile(filename string) ([]byte, error)`：读取文件内容，并返回[]byte数据和错误信息。err == nil时，读取成功

###fmt包
###os包
* `func Open(name string) (*File, error)`：打开一个需要被读取的文件，如果成功读取，返回的文件对象将可用被读取，该函数默认的权限为O_RDONLY，也就是只对文件有只读权限。如果有错误，将返回*PathError类型
* `func OpenFile(name string, flag int, perm FileMode) (*File, error)`：大部分用户会选择该函数来代替Open or Create函数。该函数主要用来指定参数(os.O_APPEND|os.O_CREATE|os.O_WRONLY)以及文件权限(0666)来打开文件，如果打开成功返回的文件对象将被用作I/O操作

const (
        O_RDONLY int = syscall.O_RDONLY // 只读打开文件和os.Open()同义
        O_WRONLY int = syscall.O_WRONLY // 只写打开文件
        O_RDWR   int = syscall.O_RDWR   // 读写方式打开文件
        O_APPEND int = syscall.O_APPEND // 当写的时候使用追加模式到文件末尾
        O_CREATE int = syscall.O_CREAT  // 如果文件不存在，此案创建
        O_EXCL   int = syscall.O_EXCL   // 和O_CREATE一起使用, 只有当文件不存在时才创建
        O_SYNC   int = syscall.O_SYNC   // 以同步I/O方式打开文件，直接写入硬盘.
        O_TRUNC  int = syscall.O_TRUNC  // 如果可以的话，当打开文件时先清空文件
)