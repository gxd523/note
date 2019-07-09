### 启动Tomcat
* 添加执行权限`chmod u+x startup.sh`、`chmod u+x catalina.sh`、`chmod u+x shutdown.sh`
* 启动tomcat`sh startup.sh`

### 常见问题
* 黑窗口一闪而过
		没有正确配置JAVA_HOME环境变量
* 端口占用
	* 找到端口`lsof -i:8080`、杀死进程`kill -9 8080`
	* 打开`config/server.xml`，搜索`8080`，更改端口号
	* `80`端口号，不用在地址中加`:80`

### 部署项目
* 往`webapps`下丢war包
* 在`config/server.xml`的，`</Host>`上一行，添加`<Context docBase="/Users/guoxiaodong/javaweb/hello" path="/hi"/>`
* 在`config/Catalina/localhost`目录下创建`hi.xml`，添加`<Context docBase="/Users/guoxiaodong/javaweb/hello" />`，访问路径为xml文件名称`hi`

### 目录结构
-- 项目根目录
	--WEB-INF目录
		--web.xml(web项目核心配置文件)
		--classes目录(放置字节码文件)
		--lib目录(放置依赖jar包)

### IDE启动Tomcat
* 在IDE`Run`中的`EditConfigurations`中`Templates`选`Tomcat Server`右边点击`Configure...`设置`Tomcat Home`目录

### IDE与Tomcat的相关配置
* 查看控制台`CATALINA_BASE`，`/Users/guoxiaodong/Library/Caches/IntelliJIdea2019.1/tomcat/Tomcat_8_5_421_HelloServlet`
* 在`out/xxx/WEB-INF`跟项目中的几乎一样，多了个`classes`目录，是真正部署的目录
* 注意：`WEB-INF`里的资源文件无法直接访问，记得放进`web`目录下