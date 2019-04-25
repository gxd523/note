[TOC]
## Jenkins安装
* [Jenkins下载](http://mirrors.jenkins.io/war/latest/jenkins.war)

* 启动Jenkins

1. `java -jar jenkins.war`

2. 浏览器打开`localhost:8080`，`cmd`打开
```
vim /Users/guoxiaodong/.jenkins/secrets/initialAdminPassword
```
输入初始密码

3. 安装建议的插件，`系统管理`-->`插件管理`-->`可选插件`-->过滤`Rebuilder`、`Safe Restart`插件

## Homebrew安装

* 安装：`/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`

* 卸载：`/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/uninstall)"`

* 

## Linux命令
* 查看IP： `ifconfig`

* 显示当前完整路径：`pwd`

* 确定可远程SSH连接

1. 检查SSH是否已连接：`netstat -anp|grep :22`、`sudo launchctl list | grep sshd`

2. 启动SSH：`service sshd start`、`sudo launchctl load -w /System/Library/LaunchDaemons/ssh.plist`、`sudo launchctl unload -w /System/Library/LaunchDaemons/ssh.plist`

3. 检查防火墙是否开启：`service iptables status`

## Java安装
## Git安装

* 验证git是否安装：`git version`

* 初始化设置

```
git config --global user.name "gxd523"
git config --global user.email "609964247@qq.com"
ssh-keygen -t rsa -C "609964247@qq.com"
```

进入SSH：`cd ~/.ssh/`、`ls`

* 公钥证书注册到Github上

1. `用户头像`-->`Settings`-->`SSH and GPG keys`-->`New SSH key`

2. `vim id_rsa.pub`，复制到github上

* 验证`ssh git@github.com`，看到

```
You've successfully authenticated
```
表示成功！

## Maven安装
* [Maven下载](http://mirror.bit.edu.cn/apache/maven/maven-3/3.5.3/binaries/apache-maven-3.5.3-bin.zip)

* 配置

1. `sudo vim /etc/profile`，修改profile文件

```
export MAVEN_HOME=/Users/guoxiaodong/apache-maven-3.5.3
export PATH=$MAVEN_HOME/bin:$PATH
``` 

2. 使配置生效：`. /etc/proifle`

3. 验证maven生效：`mvn -version`

## Tomcat安装
* [Tomcat下载](http://mirrors.shu.edu.cn/apache/tomcat/tomcat-9/v9.0.8/bin/apache-tomcat-9.0.8.zip)

* 解压zip包得到apache-tomcat-9.0.8文件夹

* 配置 

	1. 修改`conf/server.xml`中,protocol="HTTP/1.1"的port为8090

	2. 设置权限，sudo chmod -R 777 *

* 验证启动

1. bin/startup.sh

2. ps -ef|grep tomcat

3. 浏览器打开http://192.168.16.16:8090


