---
title: process
type: "categories"
categories: 后端
---

```
//默认情况下，输入流是关闭的，要监听输入流的数据，首先要开启输入流

process.stdin.resume()

let a, b

process.stout.write('请输入a的值')

//用于监听用户输入的数据

process.stdin.on('data', (chunk)=> {
	if(!a) {
		a = Number(chunk)
		process.stdout.write('请输入b的值：')
	} else {
		b = Number(chunk)
		process.stdout.write(`计算结果是： ${a} + ${b}`)
	}
})

```