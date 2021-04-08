[TOC]

## 关键词
关键词 | 含义
:---: | :---
Authorization | 授权
Authentication | 身份验证 
OAuth | Open Authorization，开放授权
Access Token | 令牌
credentials | 凭证

## OAuth(Open Authorization)
* OAuth就是一种授权机制，主要用来颁发令牌(替代密码)，以授权三方应用访问数据
* OAuth是一个验证授权开放标准

### 令牌和密码的区别
* 令牌短期有效，有过期时间，密码一般长期有效
* 令牌可以被用户撤销，立即失效
* 令牌的权限有一定的范围，例如用户只授权三方应用访问服务提供方的部分数据

### 令牌的时候
* 三方应用请求Github的用户数据时，都必须在请求头加上令牌
* `Authorization: Bearer ACCESS_TOKEN`或``authorization: Token ACCESS_TOKEN`

### 更新令牌
* 实际情况Github会返回两个令牌，一个用于获取数据，另一个用于获取新的令牌
* 获取数据的令牌到期可以请求新的令牌：
	* grant_type：refresh_token
	* client_id：Github的OAuth Apps里注册的
	* client_secret：Github的OAuth Apps里注册的
	* refresh_token：用于更新的令牌值

### 4种授权方式
#### 授权码(authorization-code)
> 第三方应用先申请一个授权码，然后再用该码获取令牌。
> 这种方式是最常用的流程，安全性也最高，它适用于那些有后端的 Web 应用。
> 授权码通过前端传送，令牌则是储存在后端，而且所有与资源服务器的通信都在后端完成。这样的前后端分离，可以避免令牌泄漏。

![](https://gitee.com/hysbtr/pic/raw/master/authorization-code.jpg)

* 用户点击三方应用提供的Github授权地址，获取授权码：
* 请求参数包括：
	* response_type：code(表示要求返回授权码)
	* client_id：Github的OAuth Apps里注册的
	* redirect_uri：回调地址
	* scope：授权范围
* 用户打开Github授权地址后，输入用户名、密码登录Github，然后同意授权，Github回调`redirect_uri`地址，并传回授权码，例如`https://a.com/callback?code=AUTHORIZATION_CODE`
* 三方应用的服务端拿到授权码，向Github请求令牌
* 请求参数包括：
	* client_id：Github的OAuth Apps里注册的
	* client_secret：Github的OAuth Apps里注册的
	* grant_type：AUTHORIZATION_CODE，授权方式为授权码
	* code：上一步拿到的授权码
	* redirect_uri：回调地址
* Github同意授权，回调`redirect_uri`地址，可通过json方式传回令牌(access_token)
#### 隐藏(implicit flow)
> 有些 Web 应用是纯前端应用，没有后端。这时就不能用上面的方式了，必须将令牌储存在前端。
> 这种方式跳过了授权码这个中间步骤，所以称为（授权码）"隐藏式"（implicit）。

![](https://gitee.com/hysbtr/pic/raw/master/implicit-code.jpg)

* 用户点击三方应用提供的Github授权地址，获取令牌：
* 请求参数包括：
	* response_type：token(表示要求直接返回令牌)
	* client_id：Github的OAuth Apps里注册的
	* redirect_uri：回调地址
	* scope：授权范围
* 用户打开Github授权地址后，输入用户名、密码登录Github，然后同意授权，Github回调`redirect_uri`地址，并传回令牌，例如`https://a.com/callback#token=ACCESS_TOKEN`
* 这种方式把令牌直接传给前端，是很不安全的。因此，只能用于一些安全要求不高的场景，并且令牌的有效期必须非常短，通常就是会话期间（session）有效，浏览器关掉，令牌就失效了。
#### 密码(password flow)
> 三方应用直接使用Github的用户名、密码申请令牌

* 用户点击三方应用提供的Github授权地址，获取令牌：
* 请求参数包括：
	* grant_type：password
	* username：用户名
	* password：密码
	* client_id：Github的OAuth Apps里注册的
* Github的返回数据包含令牌
#### 凭证(client credentials flow)
> 适用于没有前端的命令行应用，即在命令行下请求令牌

* 三方应用的命令行请求Github的授权地址，获取令牌：
* 请求参数：
	* grant_type：client_credentials
	* client_id：Github的OAuth Apps里注册的
	* client_secret：Github的OAuth Apps里注册的
* Github授权直接返回令牌