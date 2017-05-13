---
title: object变成类数组对象
type: "categories"
categories: [前端, JS, 基本概念]
---

先看JQ如何做的
```
jQuery.fn = jQuery.prototype = {
    // The default length of a jQuery object is 0
    length: 0,
    splice: arr.splice
};


```
为什么会jquery返回的是一个数组，看代码你就明白了
```
 var a = {
    id: 1,
    name: "wbcz"
  };
  for (var i = 0; i < a.length; i++) {
    console.log(a[i]) 
  }
  //没有打印，因为a.length为undefined
```
再次该进一下呢
```
 var a = {
    id: 1,
    name: "wbcz"
  };
  for (var i = 0; i < a.length; i++) {
    console.log(a[i]) 
  }
  //1
  //wbcz
  //对象获取属性的值本来就可以通过[]来获取。
```

再改进一下
```
var a = {
    0: 1,
    1: "wbcz",
    length: 2,
    splice: [].splice
};
for (var i = 0; i < a.length; i++) {
	console.log(a[i])
}
//1
//wbcz
```
