---
title: bind函数的深入实现
type: "categories"
categories: [前端, JS, 基本概念]
---

## 背景（this的绑定）

### 隐式绑定

首先要清楚调用栈和调用域的概念
```

function foo() {
	//调用栈 baz
	//调用域 baz
	console.log("foo")
}

function bar() {
	//调用栈 window
	//调用域 window
	console.log("baz")
	baz()
}

function baz() {
	//调用栈 bar
	//调用域 bar
	console.log("baz")
	foo()
}

bar()
```

所以其实

```
function foo() {
    console.log( this.a );
}

var obj2 = {
    a: 42,
    foo: foo
};

var obj1 = {
    a: 2,
    obj2: obj2
};

obj1.obj2.foo(); // 42 调用的是自己作用域的属性，即obj2

```

但是容易造成**隐式丢失**

```
function foo() {
    console.log( this.a );
}

var obj = {
    a: 2,
    foo: foo
};

var bar = obj.foo; 

var a = "oops, global"; 
bar();

```
以上你觉得会输出什么，相信你一定会说"oops, global"吧，那就对了！！！这种调用方式只是把foo的引用委托给bar了而已，其实bar的作用域还是window，所以答案就是"oops, global"，


### 默认绑定
作为一个独立函数调用
```
function foo() {
    console.log( this.a );
}

var a = 2;

foo(); // 2

```
严格模式下绑定无效
```
function foo() {
    "use strict";

    console.log( this.a );
}

var a = 2;

foo(); // TypeError: `this` is `undefined`
```

### 显示绑定（call(),apply())
```
function foo() {
    console.log( this.a );
}

var obj = {
    a: 2
};

foo.call( obj ); // 2
```
### 硬绑定
就是在显示绑定外面包了一层函数，这样之后的操作都不影响之前的绑定

```
function foo() {
    console.log( this.a );
}

var obj = {
    a: 2
};

var bar = function() {
    foo.call( obj );
};

bar(); // 2
setTimeout( bar, 100 ); // 2

// `bar` hard binds `foo`'s `this` to `obj`
// so that it cannot be overriden
bar.call( window ); // 2
```
#### 硬绑定之辅助函数（接受参数，并返回值）
```
function foo(something) {
    console.log( this.a, something );
    return this.a + something;
}

// simple `bind` helper
function bind(fn, obj) {
    return function() {
        return fn.apply( obj, arguments );
    };
}

var obj = {
    a: 2
};

var bar = bind( foo, obj );

var b = bar( 3 ); // 2 3
console.log( b ); // 5
```

### new绑定

```
function foo(n) {
    this.age = n;
    this.name = 'wbcz'
}
  
var bar =  new foo(1)

console.log(bar) // foo {age: 1, name: "wbcz"}

//如果foo原型链上也有内容，比如添加

foo.prototype.getName = function() {
    return this.name;
}
//在控制台打印出的proto中，就有getName属性

```
温习一下new关键字的时候，发生了什么
- 创建一个新的对象
- 这个对象会被[prototype]连接
- this会绑定到这个函数上
- 如果函数没有返回新的对象，那么就将this作为新的对象返回


### 优先级比较（new绑定>显示绑定>隐式绑定）

**显示绑定call vs .绑定**
```
function foo() {
    console.log( this.a );
}

var obj1 = {
    a: 2,
    foo: foo
};

var obj2 = {
    a: 3,
    foo: foo
};

obj1.foo(); // 2
obj2.foo(); // 3

obj1.foo.call( obj2 ); // 3  输出的值是obj2下作用域的属性值，显然call的优先级高
obj2.foo.call( obj1 ); // 2

```
new绑定 vs 显示绑定

```
function foo(n) {
	this.a = n
}

var obj1 = {}
var bar = foo.bind(obj1)
bar(2) 
console.log(obj1.a)  //2

var baz = new bar(3)
console.log(baz.a)  //3
console.log(obj1.a)  //2
//new修改了硬绑定调用bar()中的this
```

## bind函数的解析

有了上面的基础，来解析bind函数也就不难了，首先你要知道bind拿来做什么？没做他主要是拿来做this的指定和参数的传递，实现函数柯里化

```
function add(a, b, c) {
    var i = a+b+c;
    console.log(i);
    return i;
}

var func = add.bind(undefined, 100);//给add()传了第一个参数a
func(1, 2);//103，继续传入b和c

var func2 = func.bind(undefined, 200);//给func2传入第一个参数，也就是b，此前func已有参数a=100
func2(10);//310,继续传入c，100+200+10

//由上可以看出add被重用了，并且有初始化配置某几项，用bind先进行绑定，将复杂的函数拆分为简单的子函数
```

```
if(!Function.prototype.bind) {
	Function.prototype.bind = function (oThis) {//传递执行的作用域的对象
		if(typeof this != "function") {//如果不是bind的对象不是一个函数
			throw new TypeError("Function.prototype.bind - what is trying to be bound is not callable");
		}

		var aArgs = Array.prototype.slice.call(arguments, 1),//函数被调用执行的时候的arguments转为数组，因为第一个参数是绑定作用域的对象，所以要去掉
		fTobind = this
		fNOP = function() {}
		fBound = function() {
			return fTobind.apply(
				this instanoThisceof fNOP ? this //当用new方式调用的时候
				: oThis || this, 
				aArgs.concat(Array.prototype.slice.call(arguments)) //此处的arguments和上面的arguments不一样，最后通过concat所有函数添加到一起
			)
		}
		fNOP.prototype = this.prototype //为了将当前的函数的属性委托给新的对象,防止没法获取需要绑定的函数的属性，见如下实例
		fBound.prototype = new fNOP() 
		return fBound
	}
}

function foo(n) {
	console.log(this.a)
	return this.a + n
}

var func = foo.bind({a: 1}, 10) //1
var obj = new func({a: 1}, 40)
console.log(obj.a)  // undefined
```
