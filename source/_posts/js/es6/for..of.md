---
title: for .. of
type: "categories"
categories: [前端, JS, ES6]
---

### 让我们先来比较for..of 和for in， forEach区别

```
	//本质在于只要具有[Symbol.iterator]对象，都可以用for..of

	for..of  //可以遍历Map，Set，类数组 可break，continue, Object不能遍历， 遍历数组是值，不是索引 

	for .. in //遍历对象,主要是继承的属性也会被遍历出来，可break，continue， 遍历数组的时候是下标，非值

	forEach //不可break continue
	
```

### 既然谈到了for..of，接下来再来深入谈谈iterator

```
	1. iterator的简单实现，就是创建一个指针对象，然后调用他的next方法，直到数据结构结束的位置

	eg:

		var it = makeIterator([2,3])

		function makeIterator(arr) {
			return {
				nextIndex: 0,
				next: function() {
					return this.nextIndex < arr.length ? {value: arr[this.nextIndex++], done: false} : {value: undefined, done: true}
				}
			}
		}



	//generator函数也具有iterator特性
	function* fibonacci() { // a generator function
	    let [prev, curr] = [1, 1];
	    while (true) {
	    	[prev, curr] = [curr, prev + curr];
	    	yield curr;
	    }
	}

	for (let n of fibonacci()) {
	    console.log(n);
	    // truncate the sequence at 10
	    if (n >= 10) {
	    	break;
	    }
	}
```