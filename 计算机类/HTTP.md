# HTTP
跨域 cros  
Access-Control-Allow-Origin 设置允许访问的域名  
'Access-Control-Allow-Headers': 'X-Test-Cors',设置允许访问的自定义头  
'Access-Control-Allow-Methods': 'POST, PUT, DELETE',设置允许访问的访问  
'Access-Control-Max-Age': '1000' 以这种方式跨域请求的发送预请求的时间，1s 内不会再发生预先请求  
cros 预请求 OPTIONS  
简单请求/复杂请求

缓存  
cache-control  
public  
private  
no-cache

[https://juejin.cn/post/6844904100035821575](https://juejin.cn/post/6844904100035821575)  
**1.get & post 的区别**  
get 请求会会被浏览器主动缓存，留下历史记录  
post 更适合传输敏感信息，get 传输的信息都在 url 上  
get 请求会把报文一次性全部发出去，post 会分为两个 tcp 数据包，先发 header，再发 body  
**2.http 1.0 的特点 **  
无状态：每次请求都是独立的，无关的，不保留状态信息，在长连接的场景中，上传了大量重复信息 ->无法复用  
无连接：每次都需要重新连接 -> 队头阻塞  
明文传输  
队头阻塞  
**3.Http/1.1 使用协议最广泛的版本，如果没有标注则是 http/1.1**  
长连接 keep-alive 不需要每次都建立 tcp  
可以加缓存处理  
未解决 队头阻塞问题  
**4.http2.0**  
帧 流 消息  
二进制分帧  
多路复用  
服务器推送  
头部压缩  
**5.头部压缩原理**  
服务器 浏览器 之间  
维护一份相同的静态字典 包含常见的头部名称 和常见值  
维护一份动态字典，可以动态取值  
支持霍夫曼编码：短位表示频率大的符号，用长位表示频率小的符号  
**6.http 协议的特点**  
 http 1.0 每个 TCP 连接只能发送一个请求，请求结束，连接关闭  
 http 1.1 可保持长连接，但是容易出现队头阻塞  
 http 2.0 二进制形式分割一个个固定大小的帧，在建立的虚拟通道（流）中传递消息 多路复用  
 1.并行交错地发送多个请求，请求之间互不影响。  
 2.并行交错地发送多个响应，响应之间互不干扰。  
 3.使用一个连接并行发送多个请求和响应  
 4.压缩了 headers 服务端推送（可推送静态资源）  
**7.http 建立过程 **

- 先通过域名解析系统 DNS 将域名解析成 Ip
- 通过三次握手 建立 TCP 链接
- 发起 HTTP 请求
- 目标服务器收到请求并处理
- 发送响应
- 浏览器解析并且渲染域名

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/zxkn1f/bgbzot