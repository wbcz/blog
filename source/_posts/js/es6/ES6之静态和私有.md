---
title: ES6之静态、私有
type: "categories"
categories: [前端, JS, ES6]
---

# 静态方法和属性（实例化对象不拥有）

## 静态方法
```
class Event {
	static tell(){

	}
	get(key) {

	}
	set(key, value) {

	}
}
//调用
Event.tell()
//可以覆写
Event.tell = function() {
	
}
```
## 静态属性
两种方法设置
- constructor里面
- get、set方法（只有get没有set方法，该属性不能修改）

```
var age = 0
class Own {
	constructor({name}) {
		this.name = name //静态属性
	}
	get age() {

	}
	set age(value) {
		age = value
	}
}

babel支持静态属性下面这种写法

class Own {
  static name = 'blah'
}
```

# 私有方法和属性
私有属性的特性
- class内部不同方法间可以使用，因此this要指向实例化对象
- 私有属性不能让子类继承
- 私有属性不能被外部访问到
那么ES6如何实现私有属性呢

## 私有属性

### weakmap

```
const _name = new WeakMap()
class Own {
	constructor({name, age}) {
		_name.set(this, name)
	}
	speak() {
		_name.get(this)
	}
}
并不是理想的私有属性的方法，虽然利用weakmap可以将this作为键名，但是形式上不好看
```


### Symbol
缺点：
- 通过Reflect.ownKeys()在外部获取这些键名
- 每多一个键名，就要在外面定义一个变量
- 子类将继续拥有这些属性
```
const _name = new Symbol('name') //独一无二的
class Own {
	constuctor(name) {
		this[_name] = name
	}	
}

```
### 终极解决方法

```
let attr = {}

class Own {
	get(key) {
		return attr[this] && attr[this][key]
	}
	set(key, value) {
		if(!attributions[this]) attributions[this] = {}
		attributions[this][key] = value
	}
}
```
为私有属性的存储介质，而且每一个实例化对象都有自己的存储空间。通过get和set方法来获取和设置私有属性，非常好的解决了上面的问题。而且这种方法，如果不看源码，基本在外部是获取不到任何私有属性的，它们也不会被子类继承，当然，get和set方法是可以被继承的。

## 私有方法

理论上讲，私有属性和私有方法的区别是，私有方法是函数。因此，只需要将上面的私有属性的存储值替换为函数即可。但是作为方法，内部的this必须指向其实例化对象，因此还是需要在稍作加工。

```
var _say = new WeakMap()
class MyClass {
    constructor({name, age}) {
        this.name = name
        _say.set(this, () => {
            alert(this.name)
        })
    }
}
```
由于使用了箭头函数，函数体内的this和外部的this是同一个，因此不会发生this指向偏移的问题。

既然私有属性可以使用set, get来实现，为何私有方法不也用它来实现呢？其实，完全没有必要这样去做，就像在class外部加一个attributions一样，我们也可以在class外部创建函数，这些函数只有在同一个文档中可见，因此对外部也是私有的，外部程序无法获取。

```
let factories = {
	tell: function(options) {  // 这里一定要用function，而不能用箭头函数，因为使用箭头函数将不能使用bind
		this.render()
	}
}

class Own {
	render: function() {
		console.log('render')
	}
	call(fun) {
		if(!factories[fun]) return
		return factories[fun].bind(this)
	}
	tell() {
		this.call('tell')(options)
	}
}

//如果你不想用两个括号那么麻烦，要么不允许传入参数，要么可以用apply代替：
export default class MyClass {
    render() {}
    call(fun, ...args) {
        if(!factories[fun]) return
        return factories[fun].apply(this, args)
    }
    test() {
        this.call('a', options) // 使用call来调用私有方法，第一个括号填写函数名，第二个括号填写该函数的参数
    }
}

//如果你还想省事，甚至可以不用把函数都包含在factories里面，直接定义函数，在类里面使用apply：

function my_fun(options) {
  this.render()
}

export default class MyClass {
    render() {}
    test() {
      my_fun.apply(this, 'options')
    }
}
```
# 总结
1、 静态属性主要通过constuctor里的this绑定和自带的get、set方法实现，不需要实例化，直接调用
2、 私有属性通过set、get属性和一个中间介质变量实现了属性私有化，并且避免了被继承父属性的问题、被外部调用的问题
3、 其实apply改变函数的执行的作用域间接地相当于调用了私有方法