---
title: 代替语言内部方法的Reflect
type: "categories"
categories: [前端, JS, ES6]
---

# Reflect是什么
Reflect和Proxy是ES6提供的一套操作对象的API，目的就是为了将语言内部方法以后用Reflect代替，另外Reflect还是声明式的写法

# Reflect的静态方法
Reflect.apply(target,thisArg,args)
Reflect.construct(target,args)
Reflect.get(target,name,receiver)
Reflect.set(target,name,value,receiver) //Reflect.set会触发Proxy.defineProperty拦截
Reflect.defineProperty(target,name,desc)
Reflect.deleteProperty(target,name)
Reflect.has(target,name)
Reflect.ownKeys(target)
Reflect.isExtensible(target)
Reflect.preventExtensions(target)
Reflect.getOwnPropertyDescriptor(target, name)
Reflect.getPrototypeOf(target)
Reflect.setPrototypeOf(target, prototype)
大部分与Object对象的同名方法的作用都是相同的，而且它与Proxy对象的方法是一一对应的

