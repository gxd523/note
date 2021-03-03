[TOC]

* 用来解决网络传输中的双向通信问题
* 虽然可以在一个TCP连接上传输多个Request/Respose，但HTTP的基本模型还是一个Request对应一个Response，所以HTTP是半双工的
* WebSocket是真正意义上的全双工模式
* WebSocket是基于TCP的应用层协议
* WebSocket的scheme：`ws`、`wss`对用`http`、`https`
* 用在即时通讯，替代轮询

## HTTP双向通信的解决方案
* 轮询：造成对网络和通信双方资源的浪费，且非实时
* 长轮询：客户端发送一个超时时间很长的Request，服务器hold住这个连接，在有新数据到达时返回Response，相比#1，占用的网络带宽少了，其他类似
* 长连接：这里讲的其实是HTTP的长连接(multiple)，如果你使用Socket来建立TCP的长连接(persistent)，那么跟我们这里要讨论的WebSocket是一样的，实际上TCP长连接就是WebSocket的基础
![](https://gitee.com/hysbtr/pic/raw/master/multipleAndPersistent.png)