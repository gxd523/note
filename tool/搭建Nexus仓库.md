* 我们使用集成好的maven仓库环境Sonatype的Nexus，[点击下载](https://www.sonatype.com/download-oss-sonatype)
目录 | 作用
--- | ---
nexus-x.x.x-xx | 这个文件夹是存放应用程序的
sonatype-work | 这个文件夹是存放仓库和设置等相关内容的，如果备份数据，只用备份这个文件夹即可
./nexus-3.8.0-02/bin/nexus | 运行程序
./nexus-3.8.0-02/bin/nexus.rc | 用户配置
./nexus-3.8.0-02/etc/nexus-default.properties | 端口配置

* 启动nexus：`nexus start`
* nexus {start|stop|run|run-redirect|status|restart|force-reload}
* 浏览器打开`http://localhost:8081`
* 右上角signin，初始用户名admin、密码admin123
* 