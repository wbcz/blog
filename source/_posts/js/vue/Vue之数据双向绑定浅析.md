---
title: Vue数据双向绑定浅析
type: "categories"
categories: [前端, JS, Vue]
---

# Vue数据双向绑定浅析
>  ES5提供了一个Object.defineProperty()为其监听属性的变化
   递归监听属性的变化

```
class Observer {

	constructor(data) {

		this.watch ={} //要定义在前，防止后面访问不到

		this.data = data

		this.walk(data)

	}

	walk(obj) {

		let val

		for(let key in obj) {

			if(obj.hasOwnProperty(key)) {

				val = obj[key]

				if(typeof val === 'object') {
					new Observer(val) //监控所有属性变化
				}

				this.covert(key, val)

			}
		}
	}

	covert(key, val) {

		let self = this

		Object.defineProperty(this.data, key, {

			enumberable: true,

			configurable: true,

			get() {
				console.log(`你访问了 ${key}`);
				console.log(`值是:${JSON.stringify(val)}`)
				return val
			},

			set(newVal) {

				self.$emit(key, newVal)

				if (typeof newVal === 'object') {
					new Observer(newVal) //如果设置的值是对对象，再次递归，防止监控不到属性的变化
				}

				if (newVal === val) return;

				val = newVal
			}   
		})
	}

	$watch(key, callback) {

		if(typeof callback !== 'function') throw Error('second params must a function')
		
		(this.watch[key] || (this.watch[key] = [])).push(callback)
	}

	$emit(key, val) {
		this.watch[key] && this.watch[key].forEach(item =>{
			item(key, val)
		})
	}

}

let obj = {
	name: 'wbcz',
	age: 10,
	info: {
		con: 'china',
		university: 'js'
	}
}

let app = new Observer(obj)

app.$watch('age', function(newVal) {
	console.log(`你设置了${key}, 新的值为${newVal}`);
})

app.data.name // 你访问了name, 值是:"wbcz"
app.data.age = 20 //你设置了age, 新的值为20

```

[友情链接: 使用ES6的新特性Proxy来实现一个数据绑定实例](https://segmentfault.com/a/1190000007443611)

```
class Mog {
  constructor (options) {
    this.$data = options.data
    this.$el = options.el
    this.$tpl = options.template
    this._render(this.$tpl, this.$data)
  }

  $setData (dataObj, fn) {
    let self = this
    let once = false
    let $d = new Proxy(dataObj, {
      set (target, property, value) {
        if (!once) {
          target[property] = value
          once = true
          self._render(self.$tpl, self.$data)
        }
        return true
      }
    })
    fn($d)
  }

  _render (tplString, data) {
    document.querySelector(this.$el).innerHTML = this._replaceFun(tplString, data)
  }

  _replaceFun(str, data) {
    let self = this
    return str.replace(/{{([^{}]*)}}/g, (a, b) => {
      let r = self._getObjProp(data, b);
      console.log(a, b, r)
      if (typeof r === 'string' || typeof r === 'number') {
        return r
      } else {
        return self._getObjProp(r, b.split('.')[1])
      }
    })
  }

  _getObjProp (obj, propsName) {
    let propsArr = propsName.split('.')
    function rec(o, pName) {
      if (!o[pName] instanceof Array && o[pName] instanceof Object) {
        return rec(o[pName], propsArr.shift())
      }
      return o[pName]
    }
    return rec(obj, propsArr.shift())
  }

}

```

```
let template = document.querySelector('#app').innerHTML

let mog = new Mog({
  template: template,
  el: '#app',
  data: {
    name: 'mog',
    lang: 'javascript',
    work: 'data binding',
    supports: ['String', 'Array', 'Object'],
    info: {
      author: 'Jrain',
      jsVersion: 'Ecma2015'
    },
    motto: 'Every dog has his day'
  }
})

document.querySelector('#set-motto').oninput = (e) => {
  mog.$setData(mog.$data, ($d) => {
    $d.motto = e.target.value
  })
}
```
```
<!-- javascript -->

let template = document.querySelector('#app').innerHTML

let mog = new Mog({
  template: template,
  el: '#app',
  data: {
    name: 'mog',
    lang: 'javascript',
    work: 'data binding',
    supports: ['String', 'Array', 'Object'],
    info: {
      author: 'Jrain',
      jsVersion: 'Ecma2015'
    },
    motto: 'Every dog has his day'
  }
})

document.querySelector('#set-motto').oninput = (e) => {
  mog.$setData(mog.$data, ($d) => {
    $d.motto = e.target.value
  })
}
```
简单的实现数据绑定
```
const queuedObservers = new Set();
const observe = fn => queuedObservers.add(fn);
const observable = obj => new Proxy(obj, {
	set(target, key, value, recevier) {
		const result = Reflect.set(target, key, value, recevier); 
		queuedObservers.forEach(observer => observer());
		return result;
	}
});
```