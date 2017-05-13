---
title: DOM事件机制
type: "categories"
categories: [前端, JS, 基本概念]
---

# 漫谈
我们在node中很看见events事件操作模块， 事件操作有什么作用？为什么很多地方都存在？
- 事件操作能使复杂的业务中抽象出独立的逻辑，跨模块之间传递数据，可以达到解耦

# DOM事件
## DOM0级事件
```
<p onclick="alert(1)"></p>
```
缺点：
- HTML和JS耦合在一起
- 同一个元素不能绑定多个事件

## DOM2
el.addEventListener(eventName, callback, capture)
兼容性问题这里不多谈

## DOM3级事件
增加了很多事件类型

1. UI事件，如：scroll、load
2. 焦点事件： blur、focus
3. 鼠标事件： dbclick、mouseup
4. 滚轮事件： mousewheel
5. 文本事件： textInput
6. 键盘事件： keydown等
......

## 事件流
为什么会出现事件流，当我们给子父级同时绑定事件的时候，事件执行顺序的过程该是怎样的呢，所以这个事件执行的过程就是事件流动的过程

## 事件阶段

事件流的三个阶段：
- 捕获阶段
- 目标阶段
- 冒泡阶段


## 事件代理

绑定事件过多造成的问题
- 函数是对象，会占用内存，内存中的对象越多，浏览器性能越差
- 注册的事件一般都会指定DOM元素，事件越多，导致DOM元素访问次数越多，会延迟页面交互就绪时间。
- 删除子元素的时候不用考虑删除绑定事件
为了减少事件的注册，我们使用一个代理元素是代理它内部所有子元素事件

无论是Capture是true还是false，事件都会触发，那么事件代理和事件没有关系吗？(参考这里)[http://coderlt.coding.me/2016/11/22/js-event/]

## e.target vs e.currentTarget
- e.target返回触发事件的节点
- e.currentTarget 返回监听触发事件的节点，在冒泡和捕获阶段是非常有用的
- 在事件代理中，e.target 是当前点击的目标元素，而 e.currentTarget 始终是事件的代理元素

## 自定义事件
on，one，off，emit
one的情况单独用个数组存放
