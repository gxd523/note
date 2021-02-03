[TOC]

## 特性
* 连接池减少请求延迟
* 支持http2，同一host可共享socket
* GZIP透明传输减少下载体积
* Response缓存避免重复请求(需配置)

## 请求流程
![](https://raw.githubusercontent.com/gxd523/PictureBed/master/okhttp_request_flow_chart.png)

## 类
![](https://raw.githubusercontent.com/gxd523/PictureBed/master/OkHttpCall.png)

### OkHttpClient
* 统一管理发起请求与解析响应

### Call
* HTTP请求的抽象描述，实现类为RealCall
* 请求分为同步和异步
* 同步请求：调用`Call.exectute()`方法直接返回当前请求的Response
* 异步请求：调用`Call.enqueue()`方法将请求`AsyncCall`添加到请求队列中去，并通过回调`Callback`获取服务器返回的结果
* `RealCall.getResponseWithInterceptorChain()`：

### Dispatcher
* 整个请求、响应的流程就是`Dispatcher`不断从`Request Queue`里取出请求(`Call`)，根据是否已经存存缓存，从内存缓存或者服务器获取请求的数据
* readyAsyncCalls：准备运行的异步请求，`AsyncCall`集合
* runningAsyncCalls：正在运行的异步请求，`AsyncCall`集合，默认不超64个，同一Host的异步请求不超过5个
* runningSyncCalls：同步请求`RealCall`集合
* `maxRequests`：最多同时有64个请求
* `maxRequestsPerHost`：每个host最多同时有5个请求

### Interceptor
![](https://raw.githubusercontent.com/gxd523/PictureBed/master/okhttp_interceptor.jpg)
* 请求拦截器，拦截并处理请求
* 每个功能都是一个Interceptor，例如：请求、缓存、透明压缩等

#### InterceptorChain
* 拦截器的责任链模式

#### RetryAndFollowUpInterceptor
* 负责失败重试&重定向
* `Connections`：连接到远程服务器的物理套接字，这个套接字连接可能比较慢，所以它有一套取消机制
* `Streams`：定义了逻辑上的HTTP请求/响应对，每个连接都定义了它们可以携带的最大并发流，HTTP/1.x每次只可以携带一个，HTTP/2每次可以携带多个
* `Calls`：定义了流的逻辑序列，这个序列通常是一个初始请求以及它的重定向请求，对于同一个连接，我们通常将所有流都放在一个调用中，以此来统一它们的行为
* 实例化一个`StreamAllocation`对象，StreamAllocation相当于是个管理类，维护了`Connections`、`Streams`和`Calls`之间的管理，该类初始化一个Socket连接对象，获取输入/输出流对象
* 流程：
	1. 创建`StreamAllocation`实例
	2. 进入while循环，调用`realChain.proceed()`得到response
	3. 如果捕获到异常`RouteException`、`IOException`(与服务器连接失败)，则`continue`
	4. 未捕异常在`finally`中调用`streamAllocation.release()`，抛出异常，退出循环
	5. 未抛异常，则拿到response，调用`followUpRequest(response)`，返回request
	6. `followUpRequest()`：根据响应码处理请求，返回Request不为空表示重定向
	7. 如果为空`streamAllocation.release()`并返回response
	8. 返回request不为空，`followUpCount++`
	9. 如果重定向地址host、port、scheme已改变，重新创建`StreamAllocation`实例
	10. 返回流程2，继续循环

#### BridgeInterceptor
* 把用户构造的请求转换为发送给服务器的请求：给`Request`添加`Header`
* 把服务器返回的响应转换为对用户友好的响应：用`Okio`将`ResponseBody`解压为`RealResponseBody`(如果服务器支持`gzip`)

#### ConnectInterceptor
* 负责与服务器建立连接
* 调用`StreamAllocation.newStream()`，里面通过获取的`RealConnection`创建`HttpCodec`实例
* `HttpCodec`：用来编码HTTP requests和解码HTTP responses
* `RealConnection`：连接对象，负责发起与服务器的连接
* `RealConnection`、`HttpCodec`留给`CallServerInterceptor`用

#### CallServerInterceptor
* 负责从服务器读取响应的数据
* `RealBufferedSink`：Socket的outputStream的封装()，`Okio.sink()`
* 调用`RealBufferedSink`向服务器发送request header
* 如果有 request body，就向服务器发送
* `BufferedSource`：Socket的inputStream的封装，`Okio.source()`
* 调用`BufferedSource`，读取 response header，先构造一个 Response 对象
* 如果有 response body，就在 3 的基础上加上 body 构造一个新的 Response 对象

### Request
* 请求信息，包含：url、method、Headers、RequestBody等
* RequestBody：请求体

### Response
* 取响应信息，包含：Protocol、code、message、Headers、ResponseBody，还有Request
* ResponseBody：响应体

### 简单异步请求过程
* 创建realCall：client.newCall(request)
* call.enqueue(callback)
* client.dispatcher().enqueue(new AsyncCall(callback))
* enqueue()中：readyAsyncCalls.add(call)、promoteAndExecute()
* promoteAndExecute()中：asyncCall.executeOn(executorService)
* executeOn()中将asyncCall执行，也就是执行了asyncCall的run()调用的execute()
* execute()中：调用了realCall的getResponseWithInterceptorChain()方法获取response

## WebSocket
* Http是半双工的，同一时刻只能是请求、接收中的一个，且服务端必须等待客户端请求才能返回数据，不能主动向客户端发送数据
* WebSocket 是真正意义上的全双工模式
* WebSocket是基于TCP的应用层协议
* WebSocket的scheme：`ws`、`wss`对用`http`、`https`
* 

## Https
### X509TrustManager
* 默认非安全实现：
```java
X509TrustManager x509TrustManager = new X509TrustManager() {
    @Override
    public void checkClientTrusted(X509Certificate[] chain, String authType) throws CertificateException {
    }

    @Override
    public void checkServerTrusted(X509Certificate[] chain, String authType) throws CertificateException {
    }

    @Override
    public X509Certificate[] getAcceptedIssuers() {
        return new X509Certificate[0];
    }
};
```
### SSLSocketFactory
* 安全套接层工厂，用于创建SSLSocket
* 默认非安全实现：
```java
SSLSocketFactory sslSocketFactory = null;
try {
    SSLContext sslContext = SSLContext.getInstance("TLS");
    sslContext.init(null, new TrustManager[]{x509TrustManager}, new SecureRandom());
    sslSocketFactory = sslContext.getSocketFactory();
} catch (Exception e) {
    e.printStackTrace();
}
```
* 安全实现：
```java
// certificateInputStream = new Buffer().writeUtf8("").inputStream()
KeyStore keyStore = KeyStore.getInstance(KeyStore.getDefaultType());
keyStore.load(null);
CertificateFactory certificateFactory = CertificateFactory.getInstance("X.509");
keyStore.setCertificateEntry("certificateAlias", certificateFactory.generateCertificate(certificateInputStream));

SSLContext sslContext = SSLContext.getInstance("TLS");
TrustManagerFactory trustManagerFactory = TrustManagerFactory.getInstance(TrustManagerFactory.getDefaultAlgorithm());
trustManagerFactory.init(keyStore);
sslContext.init(null, trustManagerFactory.getTrustManagers(), new SecureRandom());
sslSocketFactory = sslContext.getSocketFactory();
```

### HostnameVerifier
* 默认非安全实现：
```java
HostnameVerifier hostnameVerifier = new HostnameVerifier() {
    @Override
    public boolean verify(String hostname, SSLSession session) {
        return true;
    }
};
```