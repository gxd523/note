[TOC]

## Http
* Hyper Text Transfer Protocol(超文本传输协议)
* 基于`TCP/IP`，默认端口号`80`
* `请求、相应模型`：一次请求对应一次响应
* `无状态`：每次请求之间相互独立，不能交互数据

### Http通信过程
![](https://raw.githubusercontent.com/gxd523/PictureBed/master/http.png)

## 请求报文
![](https://raw.githubusercontent.com/gxd523/PictureBed/master/request_message.png)

### 请求行
#### 请求方式
请求方式 | 介绍
:--: | ---
GET | 请求参数在请求行中，请求url长度有限制，不安全
POST | 请求参数在请求体中，可用来下载文件，url中没有请求参数
PUT | 向指定资源位置上传其最新内容
DELETE | 请求服务器删除 Request-URI 所标识的资源

#### 路径
* /v1/core/time

#### 请求协议/版本
* 建立连接耗时耗资源

### 请求头
名称 | 作用
:---: | ---
User-Agent | 浏览器版本信息
Content-Type | 请求体类型
Accept | 浏览器支持解析的文件格式
Accept-Encoding | 浏览器支持的压缩格式
Connection | keep-alive，请求完成后仍保留一段时间的连接 
Transfer-Encoding | chunked(分块传输)，传输编码格式，配合Content-Encoding使用 
Content-Encoding | gzip，内容编码格式，适合压缩文本 
Content-Length | 内容长度，一般不与Transfer-Encoding同时使用，因为长度不确定 
Referer | 告诉服务器，我从哪里来(1.防盗链，2.统计)

#### Content-Type
Content-Type | 解释
:---: | :--:
text/html | html格式
text/plain | 纯文本格式
text/css | CSS格式
text/javascript | js格式
image/gif | gif图片格式
image/jpeg | jpg图片格式
image/png | png图片格式
application/x-www-form-urlencoded | POST专用：普通的表单提交默认是通过这种方式。form表单数据被编码为key/value格式发送到服务器。
application/json | POST专用：用来告诉服务端消息主体是序列化后的 JSON 字符串
text/xml | POST专用：发送xml数据
multipart/form-data | POST专用：用以支持向服务器发送二进制数据，以便可以在 POST 请求中实现文件上传等功能 

#### Transfer-Encoding
* 报文中的实体改为用一系列分块来传输
* 每个分块包含十六进制的长度值和数据
* 长度值独占一行，长度不包括它结尾的`\r\n`
* 最后一个分块长度值必须为 0，对应的分块数据没有内容，表示实体结束

### 请求体
* `POST`请求才有貌似

## 响应报文
![](https://raw.githubusercontent.com/gxd523/PictureBed/master/response_message.png)

### 响应行
#### 状态码
状态码 | 含义
:---: | ---
1XX | 提示信息，表示已接收请求，正在处理
2XX | 请求正常处理完成
3XX | 重定向
4XX | 客户端错误
5XX | 服务端错误

### 响应头
* 同请求头

### 响应体
* 返回数据

## Https
![](https://raw.githubusercontent.com/gxd523/PictureBed/master/https_chart.png)

1. 客户端发起HTTPS请求，请求信息包含客户端支持的加密算法
2. 服务端的配置
服务端确认支持此加密算法。采用HTTPS协议的服务器必须要有一套数字证书，可以是自己制作或者CA证书。区别就是自己颁发的证书需要客户端验证通过，才可以继续访问，而使用CA证书则不会弹出提示页面。这套证书其实就是一对公钥和私钥。公钥给别人加密使用，私钥给自己解密使用。
3. 传送证书
这个证书其实就是公钥，只是包含了很多信息，如证书的颁发机构，过期时间等。
4. 客户端解析证书
这部分工作是有客户端的TLS来完成的，首先会验证公钥是否有效，比如颁发机构，过期时间等，如果发现异常，则会弹出一个警告框，提示证书存在问题。如果证书没有问题，那么就生成一个随即值，然后用证书对该随机值进行加密。
5. 传送加密信息
这部分传送的是用证书加密后的随机值，目的就是让服务端得到这个随机值，以后客户端和服务端的通信就可以通过这个随机值来进行加密解密了。
6. 服务段解密信息
服务端用私钥解密后，得到了客户端传过来的随机值(私钥)，然后把内容通过该值进行对称加密。所谓对称加密就是，将信息和私钥通过某种算法混合在一起，这样除非知道私钥，不然无法获取内容，而正好客户端和服务端都知道这个私钥，所以只要加密算法够彪悍，私钥够复杂，数据就够安全。
7. 传输加密后的信息
这部分信息是服务段用私钥加密后的信息，可以在客户端被还原。
8. 客户端解密信息
客户端用之前生成的私钥解密服务段传过来的信息，于是获取了解密后的内容。
PS: 整个握手过程第三方即使监听到了数据，也束手无策。

## Http版本
### HTTP/1.0
* 连接不能复用，服务端返回数据后就会断开`TCP`连接，每次请求前都要建立连接(三次握手)

### HTTP/1.1
* 服务端返回数据后不会不会立即断开`TCP`连接，每次请求复用之前还未断开的`TCP`连接
* Connection: keep-alive：保持`TCP`层连接，`HTTP/1.1`默认支持
* 服务端按序处理每个请求，前面的请求处理时间过长，会阻塞后面的请求响应

### HTTP/2.0
* 多路复用：服务端能够并发处理多个请求，避免了请求队列阻塞问题
* 二进制传输：传输都是基于字节流进行的，而不是文本，二进制分帧层处于应用层和传输层之间
* header压缩：
	* 请求头信息压缩(gzip等)后再传输
	* 客户端、服务端共同维护一张头信息表，已发送过的请求头只需发送索引即可
* 服务端推送：服务端可以对客户端的一个请求发送多个响应