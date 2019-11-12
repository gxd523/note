* 当前路径`pwd`
* 返回上一级目录`cd ../`
* 可执行文件路径`whereis git`
* 创建目录`mkdir $HOME/testFolder`
* 移动目录`mv $HOME/testFolder /var/tmp`
* 删除目录`rm -rf /var/tmp/testFolder`
* 查看目录下所有文件及文件夹`ls /etc`
* 查看文件内容`cat ~/.bash_profile`
* 目录及目录下的所有文件目录设置读写执行权限：`chmod -R a=rwx /opt/abc`
* 当前用户：`whoami`
* 几种用户，`u`：用户、`g`：用户组、`o`：其他、`a`：以上所有
* 查看文件权限：`ls -l a.txt`
* 解压：`tar xvzf android-sdk_r24.4.1-linux.tgz`
* `tar -C /usr/local -xzf go1.8.1.linux-amd64.tar.gz `
	* -A 新增压缩文件到已存在的压缩
	* -c 建立新的压缩文件
	* -d 记录文件的差别
	* -r 添加文件到已经压缩的文件
	* -u 添加改变了和现有的文件到已经存在的压缩文件
	* -x 从压缩的文件中提取文件
	* -t 显示压缩文件的内容
	* -z 支持gzip解压文件
	* -j 支持bzip2解压文件
	* -Z 支持compress解压文件
	* -v 显示操作过程
	* -l 文件系统边界设置
	* -k 保留原有文件不覆盖
	* -m 保留文件不被覆盖
	* -W 确认压缩文件的正确性
* 打印输出：`echo &PATH`
* 拷贝文件：`cp a.txt b.txt`
* 远程登录：`ssh root@118.24.27.110`
* 大概是去除证书之类的`ssh-keygen -R 118.24.27.110`
* 上传文件到服务器：`scp /Users/guoxiaodong/Desktop/docker-19.03.1.tgz root@118.24.27.110:~/docker-19.03.1.tgz`
* 查看内存：`free -h`
* 查看网络：`netstat -antup | grep`
