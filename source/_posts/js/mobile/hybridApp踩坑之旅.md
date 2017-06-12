---
title: HybridApp踩坑之旅
type: "categories"
categories: [前端, H5]
---

# native是如何和h5交互细节
(参考链接)['https://github.com/marcuswestin/WebViewJavascriptBridge']

- 导入WebViewJavascriptBridge
- UIWebView LoadURL:url 开始加载页面
- 加载完毕，植入自执行的函数（native执行一段代码： 1.挂载到window.JavascriptBridge， 2.生成一个隐藏的iframe 3. 生成一个ObjectId, obj[obj]=callback
- window加载完成之后,通过事件发出通知，h5收到WebViewJavascriptBridgeReady就可以调用了
- 前端去执行一个native函数  saveCache -》 __ELE__://saveCache?objId=1&key=1&value=2 找到对应的函数
- 通过iframe.src -> 指向Schema， 客户端就可以在一个回调函数里监听到url
- 客户端对url进行解析，判断schema. 获取方法名， 参数
- 就可以执行原生方法, 执行js函数，map[objId]

**native是拿到确切的地址，如何操作的？**
```
function(url) {

<a src='__ELE__://call?tel=123857702077'>

var isNeedle = url.indexOf('Needle://'!= 0) {
  return;
}
.....
Function
Params
Run.

}

```
**webview是啥？**
```
<Body> = RootView
<div></div> = WebView
</Body>
```

相互调用时封装的JS代码
```
/**
 * author bo.wang09@ele.me
 */

'use strict'

// H5调用native
function send(event, data = {}, callback) {

	if(typeof event !== 'string') return false

	return new Promise( (resolve, reject) => {
		needle.execute(event, data, function(err, res) {

			if(err === null) {
				typeof callback === 'function' && callback(res)
				resolve(res)
			} else {
				reject(err)
			}

		})
	})
}

//native调用H5
function register(handleName) {
	return new Promise((resove, reject) => {
	    function initJS() {
	      	needle.on(handleName, function (err, res) {
	      		if(!err) {
	      			resove(res)
	      		} else {
	      			reject(err)
	      		}
	      	})
	      	needle.execute('each.app.ready')
	    }
	    if(window.__needle__) {
	      	initJS()
	    } else {
	      	document.addEventListener('needleReady', initJS, false)
	    }
	})
}

export default {
	send,
	register
}

```

# 注意要点
1.是否页面所有DOM已经加载，必须保证h5调用方法的执行顺序在WebViewJavascriptBridgeReady挂载之后
2.sqlite数据库的使用，不能够更改表的解构
3.相机调用，dataURLtoBlob的时候注意把decodeFromBase64的内容的空白字符，包括空格、制表符、换页符替换成'',不然IOS上不兼容
4.用sqlite插入数据注意插入字符串value中要加''：`INSERT INTO each_msgHealls(dataId, type, text) VALUES('${dataId}', ${type}, '${text}')`,数据冗余的时候，注意清除数据表
5.WebViewJavascriptBridge挂载了两个方法，一个是callHandler执行函数（调用客户端），包括函数名和函数参数，最后native返回response给H5，比如拍照；一个是registerHandler监听函数(监听客户端），客户端不定时的触发任务，客户端接收到做处理，比如定位之类的需求

