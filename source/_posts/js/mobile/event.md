---
title: 移动端之Event
type: "categories"
categories: [前端, H5]
---

### 事件种类

通过obj.on添加事件 //在部分手机浏览模拟器不行
> ontouchstart
> ontouchmove
> ontouchend

通过addListener添加事件
> touchstart
> touchmove
> touchend

### 事件冒泡、捕获
> 比如分享菜单就是用hover事件实现的

### 事件的注意事项
> e.preventDefault() //在touchstart，toumove的情况下会**阻止移动端的滚动** ，在浏览器模拟器看不到效果，需要在手机上看,同时阻止页面上的文字被选中，阻止页面上的系统菜单
> 冒泡：点击元素 他会把这个事件一直向上传递；先执行子元素事件的handle，之后父元素事件也执行，用e.stopPropagation()阻止父元素事件的执行

### 事件点透
> PC端鼠标事件 在移动端也可以正常使用，但是注意 事件的执行 会有300ms的延迟

1. 在移动端 PC事件 有 300ms的延迟
2. 我们点击了页面之后，浏览器会记录点击下去的坐标
3. 300ms后，在该坐标找到现在在这的元素 执行事件

解决办法：

1. 阻止默认事件（部分安卓机型不支持)
2. 不在移动端使用鼠标事件，不用a标签做页面跳转
```
obj.addEventListener("touchend",function (e) {
		e.preventDefault();
	}
);
```

### 防止误触
```
obj.addEventListener('touchmove',()=> {
	this.move = true
})
obj.addEventListener('touchend',()=> {
	if(!this.move) {
		window.location.href = 'xxxxxx'
	}
})
```

### 事件对象
1. touches 当前屏幕上的手指列表
2. targetTouches 当前元素上的手指列表
4. changedTouches 触发当前事件的手指列表
## [请看demo](https://wbcz.github.io/special-effects/%E6%BB%91%E5%8A%A8%E7%BB%84%E4%BB%B6ES6%E5%86%99%E6%B3%95/index.html)