---
title: http基础
type: "categories"
categories: [Internet]
---

## keep-alive模式

为什么要开启keep-alive模式？
> 避免下载客户端和服务器再次建立释放连接的开销
> 单用户客户端与任何服务器或代理之间的连接数不应该超过2个。一个代理与其它服务器或代码之间应该使用不超过2 * N的活跃并发连接。这是为了提高HTTP响应时间，避免拥塞（冗余的连接并不能代码执行性能的提升）。

如何开启关闭keep-alive？
> http1.0默认是关闭的，启用需要在头上加入：Connection: Keep-Alive
> http1.1默认开启的，关闭：Connection: close
> 大多数浏览器采用的是http1.1

keep-alive模式如何确定响应数据接收完毕，我们已经知道 了，Keep-Alive模式发送玩数据HTTP服务器不会自动断开连接，所有不能再使用返回EOF（-1）来判定
- Content-Length,表示实体内容的长度，客户端可以根据这个值来判断数据是否接收完成，但是如果没有这个字段呢
- Transfer-Encoding,当客户端向服务器请求一个静态页面或者一张图片时，服务器可以很清楚的知道内容大小，然后通过Content-length消息首部字段告诉客户端 需要接收多少数据。但是如果是动态页面等时，服务器是不可能预先知道内容大小，这时就可以使用Transfer-Encoding：chunk模式来传输 数据了。即如果要一边产生数据，一边发给客户端，服务器就需要使用"Transfer-Encoding: chunked"这样的方式来代替Content-Length。
chunk编码将数据分成一块一块的发生。Chunked编码将使用若干个Chunk串连而成，由一个标明长度为0 的chunk标示结束。每个Chunk分为头部和正文两部分，头部内容指定正文的字符总数（十六进制的数字 ）和数量单位（一般不写），正文部分就是指定长度的实际内容，两部分之间用回车换行(CRLF) 隔开
- 两者都出现的时候，Transfer-Encoding优先级高

## http头字段总结
- Accept：告诉web服务器接收什么介质类型，‘/‘表示任何类型， type/* 表示该类型下的所有子类型，type/sub-type。
- Accept-Charset： 浏览器声明自己接收的字符集
- Accept-Ranges：表示服务器是否接收实体一部分（一部分）的请求，bytes：表示接收，none表示不接受
- Age： 当代理服务器用自己缓存的实体去响应请求时候，表明该实体从产生到现在有多长时间
- Content-Encoding：表明web服务器用什么了压缩方法（gzip,deflate)压缩响应的对象
- Content-Language：web服务器告诉浏览器自己响应对象的语言
- Content-Range： WEB 服务器表明该响应包含的部分对象为整个对象的哪个部分。例如：Content-Range: bytes 21010-47021/47022
- Content-Type： WEB 服务器告诉浏览器自己响应的对象的类型。例如：Content-Type：application/xml
- Expired：WEB服务器表明该实体将在什么时候过期，对于过期了的对象，只有在跟WEB服务器验证了其有效性后，才能用来响应客户请求。是 HTTP/1.0 的头部。例如：Expires：Sat, 23 May 2009 10:02:12 GMT
- Host：客户端指定自己想访问的WEB服务器的域名/IP 地址和端口号。例如：Host：rss.sina.com.cn
- If-Match：如果对象的 ETag 没有改变，其实也就意味著对象没有改变，才执行请求的动作。
- If-None-Match：如果对象的 ETag 改变了，其实也就意味著对象也改变了，才执行请求的动作。
- If-Modified-Since：如果请求的对象在该头部指定的时间之后修改了，才执行请求的动作（比如返回对象），否则返回代码304，告诉浏览器 该对象没有修改。例如：If-Modified-Since：Thu, 10 Apr 2008 09:14:42 GMT
- Last-Modified：WEB 服务器认为对象的最后修改时间，比如文件的最后修改时间，动态页面的
- If-Range：浏览器告诉 WEB 服务器，如果我请求的对象没有改变，就把我缺少的部分给我，如果对象改变了，就把整个对象给我。浏览器通过发送请求对象的 ETag 或者 自己所知道的最后修改时间给 WEB 服务器，让其判断对象是否改变了。总是跟 Range 头部一起使用。
- Range：浏览器（比如 Flashget 多线程下载时）告诉 WEB 服务器自己想取对象的哪部分。例如：Range: bytes=1173546-
-  Referer：浏览器向 WEB 服务器表明自己是从哪个 网页/URL 获得/点击 当前请求中的网址/URL。例如：Referer：http://www.sina.com/
-  Pramga：主要使用 Pramga: no-cache，相当于 Cache-Control： no-cache。例如：Pragma：no-cache
- Transfer-Encoding: WEB 服务器表明自己对本响应消息体（不是消息体里面的对象）作了怎样的编码，比如是否分块（chunked）。例如：Transfer-Encoding: chunked

## 浏览器的缓存机制
如果是请求头带有Cache-Control:max-age=0代表强制返回最新文件
如果是响应头中带有Cache-Control:max-age=0，则代表服务器要求浏览器你在使用本地缓存的时候，必须先和服务器进行一遍通信，将etag、 If-Not-Modified、If-None-Match、If-Modified-Since等字段传递给服务器以便验证当前浏览器端使用的文件是否是最新的,如果是最新的，服务端返回304，否则200，重新下载
并且确认浏览器中Disable Cache勾上没

<img src="/images/cache.png">

### 强制缓存
#### Expires
服务器返回一个**绝对时间**，然后与下次客户端请求的时间进行对比，如果未过期，直接从本地缓存获取，请求返回200（from Cache）
- 缺点： 客户端的时间与服务器的时间相差很大（时钟不同步，跨时区的因素），所以误差大，所以在HTTP1.1版开始，使用Cache-Control: max-age=秒替代

#### Cache-Control
服务器返回的是一个**相对时间**，客户端两个间隔请求之差小于Cache-Control中设置的时间差，那么就从本地缓存中取，返回 200 （from Cache)

#### 优先级
Cache-Control > Expires,Cache-Control的值可以是：

- Public 指示响应可被任何缓存区缓存。
- Private 指示对于单个用户的整个或部分响应消息，不能被共享缓存处理。这允许服务器仅仅描述当前用户的部分响应消息，此响应消息对于其他用户的请求无效。
- no-cache 指示请求或响应消息不能缓存，该选项并不是说可以设置”不缓存“，而是需要和服务器确认
- no-store 在请求消息中发送将使得请求和响应消息都不使用缓存，完全不存下來。
- max-age 指示客户机可以接收生存期不大于指定时间（以秒为单位）的响应。上次缓存时间（客户端的）+max-age（64200s）<客户端当前时间
- min-fresh 指示客户机可以接收响应时间小于当前时间加上指定时间的响应。
- max-stale 指示客户机可以接收超出超时期间的响应消息。如果指定max-stale消息的值，那么客户机可以接收超出超时期指定值之内的响应消息。

### 协商缓存

Last-Modified / If-Modified-Since ： 标识资源的最后修改时间(由服务器返回)， 配合Cache-Control使用
#### If-Modified-Since**
> 重复请求时，当强缓存失效，发现请求头中有Last-Modified声明，则添加If-Modified-Since，内容为当前请求时间，发送请求，服务器获取请求时间与资源最后修改时间对比：
如果最后个性时间大于请求时间，则说明资源更新过，返回资源文件和状态200，
如果请求时间大于最后修改时间，则说明资源未修改，返回状态304告知浏览器使用缓存

**过程**
1. 浏览器在第一次访问资源时，服务器返回资源的同时，在response header中添加 Last-Modified的header，值是这个资源在服务器上的最后修改时间，浏览器接收后缓存文件和header
2. 浏览器下一次请求这个资源，浏览器检测到有 Last-Modified这个header，于是添加If-Modified-Since这个header，值是Last-Modified中的值
3. 服务器再次收到这个资源请求，根据 If-Modified-Since 中的值与服务器中这个资源的最后修改时间对比，如果没有变化，返回304和空的响应体，如果If-Modified-Since的时间小于服务器中这个资源的最后修改时间，说明文件有更新，于是返回新的资源文件和200

缺点： 
- Last-Modified 标注的最后修改只能精确到秒级，如果某些文件在1秒钟以内，被修改多次的话，它将不能准确标注文件的修改时间（无法及时更新文件）
- 如果某些文件会被定期生成，当有时内容并没有任何变化，但Last-Modified却改变了，导致文件没法使用缓存，有可能存在服务器没有准确获取文件修改时间，或者与代理服务器时间不一致等情形（无法使用缓存）。

所以Etag / If-None-Match出现了，配合Cache-Control使用

Etag: 由服务器返回，内容为资源的唯一标识，生成规则由服务器决定。 Apache中，Etag的值为文件的索引节（INode），大小（Size）和最后修改时间（MTime）进行Hash后得到的。
If-None-Match
> 当资源过期时（使用Cache-Control标识的max-age），发现资源具有Etage声明，则再次向web服务器请求时带上头If-None-Match （Etag的值）。web服务器收到请求后发现有头If-None-Match 则与被请求资源的相应校验串进行比对：
如果相同，则返回304
如果不同，则返回200和资源文件

Etag是服务器自动生成或者由开发者生成的对应资源在服务器端的唯一标识符，能够更加准确的控制缓存。

**优先级**
Etag的优先级 大于 Last-Modified的

**过程**
1.客户端请求一个页面（A）。
2.服务器返回页面A，并在给A加上一个ETag，值是这个资源的唯一标识，由服务器端生成。
3.客户端展现该页面，并将页面连同ETag一起缓存。
4.客户再次请求页面A，并将上次请求时服务器返回的ETag一起传递给服务器。
5.服务器检查该ETag，并判断出该页面自上次客户端请求之后还未被修改，直接返回响应304和一个空的响应体。

### 用户行为与缓存
- 用户在地址栏回车、页面链接跳转、新开窗口、前进后退时，缓存是有效的
- 用户点击F5的时候，Last-Modified/Etag是有效的，但是Expires、Cache-Control重置失效
- ctrl+f5缓存全部失效

<img src="/images/userCache.png">

### 常见状态码
- 200：强缓Expires/Cache-Control存失效时，返回新的资源文件
- 304(Not Modified )：协商缓存Last-modified/Etag没有过期时，服务端返回状态码304
- 200(from cache): 强缓Expires/Cache-Control两者都存在，未过期，Cache-Control优先Expires时，浏览器从本地获取资源成功

<img src="/images/trangle.png">

### 应用
- 一般情况下，使用Cache-Control/Expires会配合Last-Modified/ETag一起使用，因为即使服务器设置缓存时间, 当用户点击“刷新”按钮时，浏览器会忽略缓存继续向服务器发送请求，这时Last-Modified/ETag将能够很好利用304，从而减少响应开销。

- 当用户在按F5进行刷新的时候，会忽略Expires/Cache-Control的设置，会再次发送请求去服务器请求，而Last-Modified/Etag还是有效的，服务器会根据情况判断返回304还是200；

- 而当用户使用Ctrl+F5进行强制刷新的时候，会跳过强缓存和协商缓存，重新从服务器下载资源。

- 分布式系统里多台机器间文件的last-modified必须保持一致，以免负载均衡到不同机器导致比对失败
分布式系统尽量关闭掉Etag(每台机器生成的etag都会不一样)

[参考](http://coderlt.coding.me/2016/11/21/web-cache/)

## 攻击

### CRSF（cross site request Forgery)跨站请求伪造
登录网站A，生成本地Cookie信息；登录危险网站B，B获取网站A的内容，并向A发送请求操作，若成功，则CRSF过程成功。其中登录B网站，行为可以是点击网站A中的链接链接。 

#### CSRF的防范
1. 验证HTTP Referer字段
2. 在请求地址中添加token并验证

### XSS攻击（跨脚本攻击）Cross-site scripting
XSS攻击基本原理——代码注入（html,sql)


