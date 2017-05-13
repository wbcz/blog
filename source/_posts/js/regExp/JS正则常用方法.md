---
title: JS正则常用方法
type: "categories"
categories: [前端, JS, regExp]
---
## 背景

1.[]里面的标点符号不需要转义
```
better: /[?+*]/ // 可读性更好
bad: [\?\+\*]
```

贪婪匹配就是在限定符后面加?
（?=pattern）
'Windows (?=95|98|NT|2000)' 能匹配 "Windows 2000" 中的 "Windows" ，但不能匹配 "Windows 3.1" 中的 "Windows"
反之：
（?!pattern)
'Windows (?=95|98|NT|2000)' 不能匹配 "Windows 2000" 中的 "Windows" ，但能匹配 "Windows 3.1" 中的 "Windows"

(?:pattern) 不获取结果的匹配，一般和（|）连用，
比如: (countr?:y|ies)



### RegExp对象的方法

#### test(str) 返回布尔值

#### exec(str) 返回匹配到的数组，如果匹配不到返回null
```
	var reg = /\d/
	var str = 'df123'
	console.log(reg.exec(str)) //["1", index: 2, input: "df123"]

	var reg = /\d/g     //加个全局结果还是一样
	var str = 'df123'
	console.log(reg.exec(str)) //["1", index: 2, input: "df123"]

	//如何取到最后一个索引呢？

	var result = []
	var reg = /\d/g
	var str = 'df123'
	var lastIndex

	while((item = reg.exec(str)) !== null) {
		lastIndex = reg.lastIndex
		result = result.concat(item) 
	}

	console.log(result) //["1", "2", "3"] 
	console.log(lastIndex)


```

### String的方法

#### match(/xxxx/) 返回匹配到的数组，如果匹配不到返回数组，返回null

```
	var reg = /\d/
	var str = 'df123'
	console.log(str.match(reg)) //["1", index: 2, input: "df123"]

	var reg = /\d/g  看到与exec有什么不同了吧
	var str = 'df123'
	console.log(str.match(reg)) //["1", "2", "3"] 


```

#### replace(/xxxx/)  返回一个新的字符串

```
	//replace中$的替换方法

	'bbb34323WWW'.replace(/([a-z]+)(\d+)([A-Z]+)/g, '$1'); // "bbb"

	//replace第二个参数为replacement，那么函数的第一个参数是其中一个匹配到的值

	'aaaa'.replace(/\w/g, function(value) {
    	return value.toUpperCase();
	}); // "AAAA"


```
#### search 匹配出索引

```
var index = 'abcb'.search(/b/g) //默认会忽略g
console.log(index)  //1 首次匹配成功的位置
```

### 案例
#### 匹配html标签

```
let re1 = new RegExp("<.+?>","g");//匹配html标签的正则表达式，"g"是搜索匹配多个符合的内容,加个？表示懒惰匹配
let msg = html.replace(re1,'');//执行替换成空字符
```

#### 匹配url参数（不含=，&,?)
```
// 1.由下可见,我们要匹配name=value
// 2.先完成name,value的匹配 /[^?&=]/
// 3.= 的正则就是 = 
// 4.将三部分组成参数对（name=value)的正则，三种情况（param=value、param=、param），正则：/([^?&=]+)(?:=[^?&=]*)*/g

function parseQueryString('?foo=123&bar') {
    var reg = /(([^?&=]+)(?:=([^?&=]*))*)/g;
    var result = {};
    var match;
    var key;
    var value;
    while (match = reg.exec(str)) {
        key = match[2];
        value = match[3] || '';
        result[key] = decodeURIComponent(value);
    }
    return result;
}

parseQueryString() //{foo: '123', bar: ''}
```
如果只匹配参数名
```
var url = 'http://www.baidu.com?name=sdf&age=12&city';

var reg = /(?:\?|&)(\w+)={0,1}/g;
var matched;
var matches = [];
while (matched = reg.exec(url)) {
    matches.push(matched[1]);
}

console.log(matches);//[ 'name', 'age', 'city' ]

```


