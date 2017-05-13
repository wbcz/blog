---
title: vue插件的写法
type: "categories"
categories: [前端, JS, Vue]
---
## 背景
vue插件是在什么时候导入的呢？vue插件是一个全局性的东西，比如toast插件来说，vue model在第一次初始化的时候会执行里面的install方法，并且只执行一次，所以刚初始化完成以后，toast插件就已经在DOM元素当中了，只是没有显示出来而已，无论我们调用show或者hide方法，不过是修改这个实例的数据和回调方法，回调函数什么时候执行呢，由watch监听show字段来决定调用时机
## 闭包导出install方法
```
(function () {

  function install (Vue) {
    Vue.YourPlugin = function (options) {

    }
  }
// 这里为了支持CMD,AMD,CommonJs,以及script标签导入的方式
  if (typeof exports == "object") {
    module.exports = install
  } else if (typeof define == "function" && define.amd) {
    define([], function(){ return install })
  } else if (window.Vue) {
    Vue.use(install)
  }

})()
```
## 为啥导出install方法，vue就能使用了呢

```
Vue.use = function (plugin) {
    /* istanbul ignore if */
    if (plugin.installed) {
      return
    }
    // additional parameters
    var args = toArray(arguments, 1)
    args.unshift(this)
    if (typeof plugin.install === 'function') {
      plugin.install.apply(plugin, args)
    } else {
      plugin.apply(null, args)
    }
    plugin.installed = true
    return this
  }
```

## npm publish 发布包

## npm install YourPlugin

[参考](http://www.jianshu.com/p/2b48887c85cc)