---
title: JavaScript的函数队列
type: "categories"
categories: [前端, JS, 基本概念]
---

##### @函数队列一开始你会想到什么，用个数组来存取一个一个函数，然后依次执行，但是函数是异步的，就不会按照我们想要的顺序执行了，可能你会想到在函数体内执行下一个函数，可惜下一个函数还没定义，怎么能执行，就算定义了，能执行，但是有个问题，我们在遍历函数的时候，是不是又重复了一遍，一下感觉没辙了，有没有？那我们换种思路吧，那么如何解决呢，node里有个connect中间件就可以实现队列，那么如何做到的呢

##### #解决方案： 定位到每个函数的末尾，然后找到下一个要执行的函数，即调用next()开始执行下一个函数

```
	1.先写个next函数吧

	let stack = []
	let index = 0
	function next() {
		let fn = stack[index]
		index += 1
		if(typeof fn === 'function') fn()
	}
```

```
	function one() {
		console.log('one')
		next()
	}
	stack.push(one)

	function two() {
		setTimeout(function(){
			console.log('two')
			next()
			//小伙伴们注意next()后面的执行的程序不能保证执行顺序了，换句话说next()一定是函数执行的末尾
		})
		//比如在这里cosole.log(xxxxx)
	}
	stack.push(two)
```

```
	//开始执行
	next()  // 打印出 one、two

```


