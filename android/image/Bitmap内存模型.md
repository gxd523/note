#### API10之前
* Bitmap自身在Dalvik Heap中，像素在Native中
* 好处是像素不占用Java层的内存，坏处是Native回收时机无法跟Java层很同步

#### API10之前
* 像素也放在Dalvik Heap中

#### API26之后
* 像素放回在Native中，因为解决了Java层Bitmap回收后及时同步Native层回收像素的问题

### 获取Bitmap占用内存大小
* getByteCount：
* 手动计算：宽(像素) x 高(像素) x 每像素点占用大小 x 压缩比例(res目录等可能会放大缩小)
* 