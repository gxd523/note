### 请求过程
1. 服务器根据url的资源路径，创建对应的Servlet对象
2. 服务器创建`Request`、`Response`对象，`Request`对象中封装请求信息
3. 服务器通过调用`service()`将`Request`、`Response`对象传递出来
4. 我们通过`Request`对象，获取请求信息，通过`Response`对象设置响应信息
5. 服务器将`Response`对象中设置的信息返回给浏览器