---
title: ES6不可忽视的问题
type: "categories"
categories: [前端, JS, ES6]
---

#### const 定义的 Array 中间元素能否被修改? 如果可以, 那 const 的意义是?
> 可以被修改，const避免类型的改变，可以保护引用不变，const保存的只是一个指针，const只能保证这个指针是固定的，至于它指向的**数据结构**是不是可变的，就完全不能控制了
