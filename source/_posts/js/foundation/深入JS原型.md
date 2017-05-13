---
title: 深入JS原型
type: "categories"
categories: [前端, JS, 基本概念]
---

### 链式关系：主要是这三条线

```
new Person() =>    __proto__    => Person.prototype =>    constructor    => function Person() =>     __proto__      => Function.prototype =>    constructor    => function Function =>........null

new Person() =>    __proto__    => Person.prototype =>   __proto__   => Function.prototype

function Function =>    prototype    => Function.prototype

```
### 总结
>   1.所有对象都有[[prototype]]属性（__proto__)，通过这个属性可以关联到对象的原型prototype;
	2.所有函数对象都有prototype属性，会赋值给该函数创建对象的[[prototype]]属性
	3.所有原型对象都有constructor属性，指向该原型的构造函数
	4.函数对象和原型对象通过constructor和prototype相互关联


### 原型继承的注意事项

#### 一

> Bar.prototype = Foo.prototype 并不会创建一个关联到Bar.prototype 的新对象，它只
是**让Bar.prototype 直接引用Foo.prototype 对象**。因此当你执行类似Bar.prototype.
myLabel = ... 的赋值语句时会直接修改Foo.prototype 对象本身。显然这不是你想要的结
果，否则你根本不需要Bar 对象，直接使用Foo 就可以了，这样代码也会更简单一些。

> Bar.prototype = Foo.prototype **这个也将会导致两个对象共享相同的原型**
简而言之，就是 new Bar() 不会创造出一个新的 Foo 实例，而是 重复使用它原型上的那个实例；
因此，所有的 Bar 实例都会共享相同的 value 属性。
3.思考： 为什么不能 Bar.prototype = Foo，因为这不会执行 Foo 的原型，而是指向函数 Foo。 
因此原型链将会回溯到 Function.prototype 而不是 Foo.prototype，因此 method 将不会在 Bar 的原型链上

#### 二

> Bar.prototype = new Foo() 的确会创建一个关联到Bar.prototype 的新对象。但是它使用
了Foo(..) 的“构造函数调用”，**如果函数Foo 有一些副作用（比如写日志、修改状态、注
册到其他对象、给this 添加数据属性，等等）的话，就会影响到Bar() 的“后代”**，后果
不堪设想。

#### 三

> 要创建一个合适的关联对象，我们必须使用Object.create(..) 而不是使用具有副
作用的Foo(..)。这样做唯一的缺点就是**需要创建一个新对象然后把旧对象抛弃掉，不能
直接修改已有的默认对象**。


### 原型继承的解决方案

>  就是通过一个标准且可靠的方法来修改对象的[[Prototype]] 关联就好了
	1.设置.\__proto\__ 属性来实现，but不标准，不能兼容所有浏览器
	2.ES6 提供的 Object.setPrototypeOf( Bar.prototype, Foo.prototype )，标准并且可靠
	3.利用Object.create(Foo.prototype)

```
Object.prototype.create = function (o) { 
	function F(){}
 	F.prototype = o;
	return new F();
}

```


### 属性查找

> 属性查找时都会查找 [[Prototype]] 链，直到找到属性或者
查找完整条原型链,找不到，返回undefined

### 属性设置

> 假如 myObject.foo = "bar";


1. 如果原型链上找不到 foo，foo 就会被直接添加到 myObject 上。
2. 属性名 foo 既出现在 myObject 中也出现在 myObject 的 [[Prototype]] 链上层, myObject 中包含的 foo 属性会屏蔽原型链上层的所有 foo 属性，因为 myObject.foo 总是会选择原型链中最底层的 foo 属性
3.foo 存在于原型链上层
> 如果在[[Prototype]]链上层存在名为foo的普通数据访问属性(参见第3章)并且没 有被标记为只读(writable:false)，那就会直接在 myObject 中添加一个名为 foo 的新 属性，它是屏蔽属性
如果在[[Prototype]]链上层存在foo，但是它被标记为只读(writable:false)，那么 无法修改已有属性或者在 myObject 上创建屏蔽属性。如果运行在严格模式下，代码会 抛出一个错误。否则，这条赋值语句会被忽略。总之，不会发生屏蔽。
如果在[[Prototype]]链上层存在foo并且它是一个setter(参见第3章)，那就一定会 调用这个 setter。foo 不会被添加到(或者说屏蔽于)myObject，也不会重新定义 foo 这 个 setter。


### 一些想关的原型方法 
```
1. b.isPrototypeOf( c );  // 非常简单:b 是否出现在 c 的 [[Prototype]] 链中
2. Object.getPrototypeOf( a ) === Foo.prototype; // true
3. a.__proto__  === Foo.prototype; // true  绝大多数(不是所有!)浏览器也支持一种非标准的方法来访问内部 [[Prototype]] 属性
```
>	和我们之前说过的 .constructor 一样，.__proto__ 实际上并不存在于你正在使用的对象中 (本例中是 a)。实际上，它和其他的常用函数(.toString()、.isPrototypeOf(..)，等等),
一样，存在于内置的 Object.prototype 中

**.__proto__ 的实现大致上是这样的:**

```
Object.defineProperty( Object.prototype, "__proto__", { 
	get: function() {
		return Object.getPrototypeOf( this ); 
	},
	set: function(o) {
		// ES6 中的 setPrototypeOf(..) 
		Object.setPrototypeOf( this, o ); 
		return o;
	} 
});

//因此，访问(获取值)a.__proto__ 时，实际上是调用了 a.__proto__()(调用 getter 函 数)。虽然 getter 函数存在于 Object.prototype 对象中，但是它的 this 指向对象 a，所以和 Object.getPrototypeOf( a ) 结果相同。
```

### instanceof 和 isPrototypeof的区别
```
a.isPrototypeof(new B()) 测试一个a是否存在于B的原型链上。

a instanceof B  对象a在其的原型链中是否存在一个构造函数的prototype属性
```


	


