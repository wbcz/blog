---
title: vue组件和router的生命周期注意要点
type: "categories"
categories: [前端, JS, Vue]
---

写了这么久的vue了，发现没啥笔记，今天抽空来写个简单的博客吧

## vue组件的生命周期

### beforeCreate 
完成实例化，未完成数据的监测，指令的绑定等操作

### created
实例创建完成之后调用（完成数据的观测，属性方法的运算，watch事件的回调），还未开始挂载

### beforeMount
挂载前的状态，已经被初始化

### mounted 
挂载结束，el 被新创建的 vm.$el 替换，并挂载到实例上去之后调用该钩子。如果 root 实例挂载了一个文档内元素，当 mounted 被调用时 vm.$el 也在文档内。
只执行一次

### beforeUpdate
更新前的状态

### updated
更新后的状态

### activated
keep-alive 组件激活的时候调用，所以如果你想不缓存这个组件，那么数据可以从这里获取，不用从created中获取,还有另外一种方式，在route的配合文件里单独配置meta信息，然后在app.vue进行处理

### deactivated
组件停用时调用

### beforeDestroy
实例销毁前调用

### destroyed
实例销毁后调用,销毁watchers, listeners,child components


## vue-router的router的生命周期

### beforeEnter
可以用来做用户的验证

### beforeRouteEnter
处于激活状态，这个阶段你可以设置标题啊，但是必须执行一个next的回调，之后的操作才会继续进行
这里可以开启一个loading

### $route
watch监听$route数据，然后再调用 transition.next()进行数据的展示; 
结束loading

### beforeRouteLeave 
登出验证

### beforeDestroy
当组件被禁用和移除时候调用

### 总结
生命周期简单的说就是创建前，挂载前，更新前，销毁前
