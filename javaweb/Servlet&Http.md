### Servlet
* 全称Server applet(服务端小程序)
* Servlet对象都是通过反射生成的
* Servlet的运行要依赖于Web容器，Tomcat就是Web容器
* `<load-on-startup>`<0时，servlet第一次被访问时才创建，>=0时，服务器启动时创建
* Servlet是单例的，多个用户同时访问时可能存在线程安全问题，加锁太影响性能，尽量不要在Servlet中定义成员变量，即使定义了也不要对其进行修改，保证只读不写。
* destroy()在Servlet销毁前调用，一般用于释放资源
* `GenericServlet`：空实现了除`service()`之外的其他方法，是个抽象类
* `HttpServlet`：支持Http协议， 只需重写`doGet()`、`doPost()`
* `urlPattern`可以是`/xx/xx`形式，也可以是`*.do`或`aa.bb`这种形式