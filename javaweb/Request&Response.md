### 请求过程
1. 服务器根据url的资源路径，创建对应的Servlet对象
2. 服务器创建`Request`、`Response`对象，`Request`对象中封装请求信息
3. 服务器通过调用`service()`将`Request`、`Response`对象传递出来
4. 我们通过`Request`对象，获取请求信息，通过`Response`对象设置响应信息
5. 服务器将`Response`对象中设置的信息返回给浏览器

### 功能
* 获取请求数据
	* 请求行数据![](https://gitee.com/hysbtr/pic/raw/master/request_header_row.png)
		* getMethod()：GET
		* getContextPath()：/javaweb
		* getServletPath()：/simplehttp
		* getQueryString()：username=gxd&age=18
		* getRequestURI()：/javaweb/simplehttp
		* getRequestURL()：http://localhost:8080/javaweb/simplehttp
		* getProtocol()：HTTP/1.1
		* getRemoteAddr()：0:0:0:0:0:0:0:1
	* 请求头数据
	* 请求体数据(post)
* 