---
title: this隐式丢失
type: "categories"
categories: [前端, JS, 基本概念]
---

### this的上下文

```
let person = {
	count: 1,
	getCount: function() {
		return this.count;
	}
}

console.log(person.getCount())

//但是当:

  let func = person.getCount //this 丢失了

  console.log(funct())  // undefined

 //如何解决呢

 let func = person.getCount.bind(person)

 	console.log(funct())  // 1

```


```

Function.prototype.bind = Function.prototype.bind || function(context) {
 	let self = this
 	return function() {
 		let args = Array.prototype.slice.call(arguments)
 		self.apply(context, args)
 	}
}

let person = {
	count: 1,
	getCount: function() {
		console.log(arguments) //[2, 6]
		return this.count;
	}
}

let func = person.getCount.bind(person)

func(2,6)

```