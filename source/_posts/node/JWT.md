---
title: JWT
type: "categories"
categories: 后端
---

# JWT
http协议是无状态的，所以会有cookie/session来解决状态问题，但是随着终端设备增多，前后端分离方式流行，RESTful API是目前比较成熟的一套接口规范，提倡无状态，JWT可以实现无状态，避免了session这种有状态的，大量的占用服务器的内存，需要借助于redis集群来存储session，JWT将用户状态权限放到客户端，服务端根据传递过去的的token来判断是否有访问这个资源的权限

# JWT组成部分
- 头部
- 负载
- 签名
token经过对头部，负载进行base64处理，再将头部，负载，签名通过加密算法进行加密，最后生成token = 头部.负载.签名

[参考](https://segmentfault.com/a/1190000009030769)