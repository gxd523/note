### 远程登录服务器
* `ssh root@118.24.27.110`
* 大概是去除证书之类的`ssh-keygen -R 118.24.27.110`

### 安装Java环境
* `yum -y install java-1.8.0-openjdk-devel`

### 安装Git
* `yum -y install git`

### 安装Jenkins仓库
* `sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo`

* `sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key`
如果您以前从 Jenkins 导入过 key，那么 rpm --import 将失败，因为您已经有一个 key。请忽略，继续下面步骤。

* `yum -y install jenkins`

### 启动 Jenkins 并设置为开机启动
* `systemctl start jenkins.service`
* `chkconfig jenkins on`

### 登陆Jenkins
* `cat /var/lib/jenkins/secrets/initialAdminPassword`，复制密码
* 启动Jenkins`http://118.24.27.110:8080/`，输入复制的初始密码

### 安装Jenkins插件
* 我们选择默认的`install suggested plugins`来安装插件。

### 安装Android SDK
* 下载Android SDK`wget https://dl.google.com/android/android-sdk_r24.4.1-linux.tgz`
* 解压`tar xvzf android-sdk_r24.4.1-linux.tgz -C /opt`
* 权限设置`chmod -R a+rwx /opt/android-sdk-linux`
* 配置环境变量`vim ~/.bash_profile`，并`source ~/.bash_profile`是环境变量生效
```
ANDROID_HOME=/opt/android-sdk-linux
PATH=$PATH:$ANDROID_HOME/tools:$ANDROID_HOME/platform-tools
```
### 安装Android SDK组件
* 显示所有的sdk版本`android list sdk --all`
* 然后选择想要更新的版本的编号`android update sdk -u -a -t 1,2,4,48,197,212,220`