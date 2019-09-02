> 每一个部署在Docker上的应用都像一个集装箱，集装箱内部是未知的，集装箱之间相互隔离

### Docker特点
* 容器(container)引擎
* 打包/发布应用程序，包里面包括系统环境、依赖、配置
* 虚拟化、沙箱机制

### 下载安装
* community edition:地址：https://hub.docker.com/editions/community/docker-ce-desktop-mac
* 国内镜像：http://get.daocloud.io/#install-docker-for-mac-windows
* 修改mirror：https://www.daocloud.io/mirror
* Linux下安装：`sudo wget -qO- https://get.docker.com | sh`

### Docker命令
* 查看版本：`docker version`
* 查看信息：`docker info`
* 查看已安装镜像：`docker images`
* 安装一个最小的linux镜像：`docker run -it alpine sh`
* 安装`nginx`web服务器：`docker run -d -p 80:80 nginx`(-d:deamon一直运行，-p端口，容器和物理机器隔离，所以要把容器里nginx运行的80端口映射到物理机器的80端口)
* 查看容器列表：`docker ps`
* 关闭容器：`docker kill 6ed3c3b19728`
* 安装elasticsearch：`docker pull elasticsearch:6.8.2`
* 运行elasticsearch：`docker run -d -p 9200:9200 elasticsearch:6.8.2`
* 低内存：`docker run -d -p 9200:9200 -e ES_JAVA_OPTS="-Xms100m -Xmx100m" elasticsearch:5.2.1`
* 启动docker：`service docker start`
* 开机启动docker：`chkconfig docker on`

