[TOC]

##  设置
* 打开`SSL Proxying Settings`：
	* 勾选`Enable SSL Proxying`
	* `Include->Location`添加`Host`不用填，`Port`为`443`
* 打开`Proxy Settings`，`Port`填`8888`

## 安装证书
### PC端安装证书
* 打开`Help`里的`SSL Proxying`，选择`Install Charles Root Certficate`
* 打开`钥匙串访问`，在`系统`中找到`Charles Proxy CA`并打开，打开`信任`下拉框，`使用此证书时`选择`始终信任`

### 手机端安装证书
* 打开`Help`里的`SSL Proxying`，选择`Save Charles Root Certficate`，保存证书
* 将Mac上的证书导入手机中，在手机的`设置`-->`安全`-->`从存储设备安装`，选择从Mac导入手机的pem格式的证书(pem不行可以将后缀改为.crt试试)

## 手机代理设置
* `Charles`的`Help`里的`LOcal IP Address`查看`PC端的IP地址`
* 和PC连接相同Wifi
* Wifi设置里勾选`修改网络`里的`高级选项`，代理地址为`PC端的IP地址`，端口为`Proxy Settings`里设置的`8888`