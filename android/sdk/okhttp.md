* okhttp是基于HttpURLConnection设计的
* 连接池减少请求延迟
* 支持http2，同一host可共享socket
* GZIP透明传输减少下载体积
* Response缓存避免重复请求

### 简单异步请求过程
* 创建realCall：client.newCall(request)
* call.enqueue(callback)
* client.dispatcher().enqueue(new AsyncCall(callback))
* enqueue()中：readyAsyncCalls.add(call)、promoteAndExecute()
* promoteAndExecute()中：asyncCall.executeOn(executorService)
* executeOn()中将asyncCall执行，也就是执行了asyncCall的run()调用的execute()
* execute()中：调用了realCall的getResponseWithInterceptorChain()方法获取response