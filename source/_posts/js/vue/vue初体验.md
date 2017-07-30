title: VUE初体验
speaker: wbcz
url: https://github.com/wbcz
transition: cards
files: /css/index.css

[slide data-transition="vertical3d"]

# vue初体验
# 演讲者：wbcz

[slide]

# 先来道js基础题尝尝鲜

```
for(var i = 0;i < 5;i++) {
	console.log(i)
}
```
```
for(var i = 0;i < 5;i++) {
	setTimeout(function() {
		console.log(i)
	}, 1000*i)
}
```
```
for(let i = 0;i < 5;i++) {
	setTimeout(function() {
		console.log(i)
	}, 1000*i)
}
```

[slide]

```
for(var i = 0;i < 5;i++) {
	(function(i) {
		setTimeout(function() {
			console.log(i)
		}, 1000*i)
	})(i)
}
```
```
for(var i = 0;i < 5;i++) {
	(function(i) {
		setTimeout(function() {
			console.log(i)
		}, 1000*i)
	})()
}
```

[slide]
# Vue简单介绍 {:&.fadeIn}
## 1.数据驱动的渐进式框架
## 2.与Jquery库不同之处

[slide]

# 模板语法

```
<span>{{user.name}}</span>
<span>{{user.age | number}}</span>
<span>{{user.age + 1}}</span>
<span>{{user.age ? user.age : 18 }}</span>
```

[slide]

# 计算属性

- 计算属性是基于它们的依赖进行缓存的

```
//Date.now() 不是响应式依赖
computed: {
	now: function () {
		return Date.now()
	}
}
```
```
	//name 值是依赖的
	data() {
		return {
			name: 'wbcz'
		}
	}
	computed: {
		now: function () {
			'my name is ' + this.name
		}
	}
```

[slide]

# computed vs watch

声明式 vs 命令式

```
  computed: {
    fullName: function () {
      return this.firstName + ' ' + this.lastName
    }
  }
```

```
  watch: {
    firstName: function (val) {
      this.fullName = val + ' ' + this.lastName
    },
    lastName: function (val) {
      this.fullName = this.firstName + ' ' + val
    }
  }
```

[slide]
# Class 与 Style 绑定
v-bind:class=""  v-bind:style=""

# 条件渲染 {:&.fadeIn}
v-if v-show 

# 列表渲染
v-for

# 事件处理器
v-on:click="say()"

# 表单控件
v-model=""

[slide]

# 生命周期 {:&.fadeIn}
## beforeCreate 
## created
## beforeMount
## mounted 
## beforeUpdate 
## updated
## beforeDestroy
## destroyed