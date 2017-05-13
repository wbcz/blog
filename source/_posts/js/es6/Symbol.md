---
title: ES6 Symbol
type: "categories"
categories: [前端, JS, ES6]
---

### Symbol的由来

保证对象的属性不重复

#### Symbol 类型(有点类似于字符串)

```
let sys = Symbol('test')
typeof sys  //"symbol"
```
#### Symbol的比较

```
let sys = Symbol('test')
let ss = Symbol('test')
sys === ss //false

let r = Symbol()
let ss = Symbol()
r === ss  //false

结论： Symbol函数的参数只是表示对当前 Symbol 值的描述

```
#### symbol与其他类型的不同点

```
let sys = Symbol('test')
sys + 222  //TypeError //不能隐式的其他的函数进行运算

Boolean(sys) // true
String(sys) // "Symbol(test)"
Number(sys)  //TypeError

//显示转换只有Number报错

```
### 作为属性名的Symbol

```
为了保证属性不重复，如此定义

let sys = Symbol('prop1')
let obj = {
	[sys]: 'name'
}
obj[sys]
```

#### 使用
```
//switch中的常量不等

const COLOR_RED    = Symbol();
const COLOR_GREEN  = Symbol();

function getComplement(color) {
  switch (color) {
    case COLOR_RED:
      return COLOR_GREEN;
    case COLOR_GREEN:
      return COLOR_RED;
    default:
      throw new Error('Undefined color');
    }
}

```
