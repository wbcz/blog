---
title: 构建web应用
type: "categories"
categories: 后端
---

# 需求
1.请求方法的判断
2.URL路径解析
3.URL中查询字符串解析
4.Cookie的解析
5.Basic的认证
6.表单数据的解析
7.任意格式文件的上传处理

## 请求方法
通过HTTP_Parser解析报文，将报文头抽取出来，设置为req.method

## 路径解析
通过HTTP_Parser解析成req.url,比如根据路径去找到静态资源

## 查询字符串
node提供了querystring模块用于处理这部分数据


## cookie
cookie发送到服务端的几个选项
- Path，表示这个cookie影响到的路径，当前访问路径不满足该匹配的时候，浏览器不发送Cookie
- Expire和Max-Age用来告诉浏览器这cookie何时过期，如果不设置，关闭浏览器时候会丢掉这个cookie，后者是个相对时间，优先级更高
- HttpOnly 告知浏览器不允许通过脚本document.cookie
- Secure 表示只有https的时候才会传递cookie

### cookie的性能影响
- 减少cookie的大小，如果在根域名设置cookie，几乎所有子路径下的请求都会带上cookie，但是某些静态页面是不需要的
- 为静态资源使用不同的域名，不过这会造成一个缺点，将域名转换成IP需要进行DNS查询，多一个域名就多一次DNS查询
- 减少DNS查询，如今浏览器会对DNS进行缓存


