---
title: promise解读
type: "categories"
categories: [前端, JS, ES6]
---
### promise是什么

promise是对异步操作的封装，可以通过独立的接口在异步操作执行成功和失败的方法

### 原理

promise通过then让异步函数按照你的规定的顺序进行，.then的时候放入下一个事件，当异步操作完成时，resolve触发事件队列中的事件，便完成了一个.then操作，
所以每当我们异步操作完成的时候就移除事件队列的这个事件，通过resolve使事件持续触发下去,核心如下：
- 定义一个需要传递给下面事件的value
- 定义一个events，维持一个事件队列
- 定义一个then函数，把需要执行的函数存入事件队列
- 定义一个resolve函数，移除已经执行完的函数，并且触发下面的事件继续执行，并传递value
- 要保证promise里面是一个异步操作，所以需要加setTimeout将执行回调的逻辑放置到 JS 任务队列末尾

```
function Promise(fn) {

	var events = []
	var value = null

	this.then = function (f) {
		events.push(f)
		return this
	}
	function resolve(newValue) {
		setTimeout(function() {
			var f = events.shift()
			f(newValue, resolve)
		}, 0)
	}

	fn(resolve)
}	
```

