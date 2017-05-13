---
title: promise、co、gennerator、async深入理解
type: "categories"
categories: 后端
---

## promise注意要点：

[实践案例移步](https://github.com/wbcz/untils)

## Event Loop Queue(事件循环队列)

```
setTimeout(() => {
  console.log("B");
}, 1000);
```
setTimeout 延迟 1000 不一定是准确的,因为如果还有其他的任务在前面，它要等待那些任务对应的消息都出队，也就是程序都执行完成，它才能将 callback 放入队列。也就是实际延迟会大于或等于一秒。

Event Loop Queue中存放的都是消息，每个消息关联着一个函数，JavaScript Engine 就按照队列中的消息顺序执行它们，也就是执行 chunk。
```
try {
  setTimeout(() => {
    throw new Error("Error - from try statement");
  }, 0);
} catch (e) {
  console.error(e);
}
//try catch 与 setTimeout 不在同一个 chunk，所以捕捉不到错误
```

```
//下面的堆栈信息会输出 C - B - A 吗？
setTimeout(function A() {
  setTimeout(function B() {
    setTimeout(function C() {
      throw new Error("Error - from function C");
    }, 0);
  }, 0);
}, 0);
//它们并不对应同一条 Event Loop Queue 中的消息，分别有各自的调用栈，所以错误栈里面只有 C。
```

## Job Queue
Job 是 ES6 中新增的概念，它与 Promise 的执行有关，可以理解为等待执行的任务；Job Queue 就是这种类型的任务的队列。JavaScript Runtime 对于 Job Queue 与 Event Loop Queue 的处理有所不同。

相同点:
- 都用作先进先出队列
不同点：
- 每个 JavaScript Runtime 可以有多个 Job Queue，但只有一个 Event Loop Queue
- 当 JavaScript Engine 处理完当前 chunk 后，优先执行所有的 Job Queue，然后再处理 Event Loop Queue
ES6 中，一个 Promise 就是一个 PromiseJob，一种 Job。
```
console.log("A");

setTimeout(() => {
  console.log("A - setTimeout");
}, 0);

new Promise((resolve) => {
  resolve();
})
.then(() => {
  return console.log("A - Promise 1");
})
.then(() => {
  return console.log("B - Promise 1");
});

new Promise((resolve) => {
  resolve();
})
.then(() => {
  return console.log("A - Promise 2");
})
.then(() => {
  return console.log("B - Promise 2");
})
.then(() => {
  return console.log("C - Promise 2");
});

console.log("AA");

```
答案：
```
A
AA
A - Promise 1
A - Promise 2
B - Promise 1
B - Promise 2
C - Promise 2
A - setTimeout
```
解答：
- A 与 AA 最先输出，因为它们不是异步任务，属于第一个 chunk。

- Promise 1 与 Promise 2 先于 setTimeout 执行，因为 Job Queue 的执行优先于 Event Loop Queue。

- Promise 1 与 Promise 2 各自的输出都是顺序的，因为 Job Queue 是先进先出队列，同一 Job Queue 中的任务顺序执行。

- Promise 1 与 Promise 2 的后续任务是交错的，因为 Promise 1 与 Promise 2 都是独立的 PromiseJob（job 的其中一种），属于不同的 Job Queue，它们之间的顺序规范中没有规定。

```
	1.返回新的回调加return
	2.then参数不要传递类似promise.resolve()这样的参数，会被解析为null，尽量采用function() {}的方式返回
	3.捕捉错误，用catch，同时catch函数是作为then单独的一个函数，不要then里面包含其他的函数，防止捕捉不到错误
	4.多层循环里面的异步中的异步，用co函数和promise配合解决
```
#### [async和generator](http://www.ruanyifeng.com/blog/2015/05/async.html)

```
	//async并发异步操作
	function go() {
		setTimeout(function() {
			console.log('go')
		},2000)
	}

	function go1() {
		setTimeout(function() {
			console.log('go1')
		},3000)
	}

	function go2() {
		setTimeout(function() {
			console.log('go2')
		},1000)
	}

	async function test() {
	  let docs = [go, go1, go2];

	  for (let doc of docs) {
	    await doc();
	  }
	}

	test()

```

```
	//co函数的并发操作
	
	co(function* () {

	    var res = yield [
	    	Promise.resolve(1),
	    	Promise.resolve(2)
	    ];

	  	console.log(res);

	}).catch(onerror);

```
