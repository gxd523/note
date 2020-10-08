[TOC]

## Http
* Hyper Text Transfer Protocol(超文本传输协议)
* 基于`TCP/IP`，默认端口号`80`
* `请求、相应模型`：一次请求对应一次响应
* `无状态`：每次请求之间相互独立，不能交互数据

### Http通信过程
![](https://gitee.com/hysbtr/pic/raw/master/http.png)

## 请求报文
![](https://gitee.com/hysbtr/pic/raw/master/request_message.png)

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
* `1.0`：每次请求前都要建立连接，响应后都要断开连接
*  `1.1`：每次请求前复用之前的连接(如果有)、响应后过一会再断开连接
* 建立连接耗时耗资源

### 请求头
名称 | 作用
:---: | ---
User-Agent | 浏览器版本信息
Content-Type | 请求体类型
Accept | 浏览器支持解析的文件格式
Accept-Encoding | 浏览器支持的压缩格式
Connection | keep-alive表示可以复用
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

### 请求体
* `POST`请求才有貌似

## 响应报文
![](https://gitee.com/hysbtr/pic/raw/master/response_message.png)

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
### 第一次https请求
* 客户端向服务端发起请求，请求信息包含客户端支持的加密算法，服务端确认支持此加密算法并返回公钥
* 客户端验证公钥，并生成对称加密密钥使用公钥加密，发送给服务端
* 服务端用私钥解密拿到对称加密密钥，然后用对称加密密钥给数据加密发送给客户端

### 第二次https请求
* 