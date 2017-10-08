---
title: cookie
type: "categories"
categories: 后端
---

# cookie
1. userId 不能单独用userId，不安全
2. userId + 签名（防止篡改）  crypto
3. sessionId 不存储用户信息

# xss 与 cookie
xss可以获取cookie，通过document.cookie,但是在服务端设置httpOnly: true，js就不能获取了

# cookie 与csrf
csrf可以利用cookies，攻击站点无法读写cookie，所以可以采用验证码进行防御，还有一种方式就是禁止第三方利用cookie

# cookie安全策略
1.签名是防止篡改
2. 私有变换（加密）

