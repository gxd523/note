## 第一步：Tomcat安装
* 进入[Tomcat官网](https://tomcat.apache.org/)，左侧`Download`下选择Tomcat9，右侧`Binary Distributions`下`Core`下选择zip包下载
* 解压zip包得到apache-tomcat-9.0.8文件夹，配置 
	1. 修改`conf/server.xml`中,protocol="HTTP/1.1"的port为8090
	2. 设置bin目录及其子目录文件的可执行权限`chmod -R u+x /User/guoxiaodong/apache-tomcat-x.x.x/bin`
* 在bin目录下输入`startup.sh`启动Tomcat，浏览器输入http://localhost:8090验证

## 第二部：Jenkins安装
* 进入[Jenkins官网](https://jenkins.io)，切换成中文，点击`下载`选择`Generic Java Package(.war)`下载
* 将jenkins.war直接丢进Tomcat的`webapps`目录
* 启动Tomcat，浏览器打开http://localhost:8090/jenkins
* 第一次启动Jenkins需要输入初始密码，在`/Users/guoxiaodong/.jenkins/secrets/initialAdminPassword`文件中
* 安装如下插件:
	* Gradle Plugin
	* xxx
* 配置环境变量：进入Jenkins，左侧选择`系统管理`，右侧选择`系统设置`，在右侧`全局属性`下勾选`环境变量`，键输入`ANDROID_SDK_ROOT`，值输入`/Users/guoxiaodong/Library/Android/sdk`
* 新建任务：左侧选择`新建任务`，输入人物名称，选择`构建一个自由风格的软件项目`

## Homebrew安装

* 安装：`/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`

* 卸载：`/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/uninstall)"`