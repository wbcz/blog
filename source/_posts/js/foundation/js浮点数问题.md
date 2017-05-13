---
title: js浮点数问题
type: "categories"
categories: [前端, JS, 基本概念]
---

```
### 对精度要求不高可用toFixed(),toPrecision()，
### 或者转换成整数
### 或者移步math.js

```
```
(function() {
	console.log(0.1 + 0.2 === 0.3)  //false
}())

Number.prototype.floatCalculate = Number.prototype.floatCalculate || function(number=10) {
	//number should <= 16
	return this.toFixed(number)
};
```