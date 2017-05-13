---
title: 浏览器内核(渲染引擎，js引擎)
type: "categories"
categories: [前端, JS, 基本概念]
---

浏览器内核分为渲染引擎和js引擎，由于js引擎越来越独立，内核就倾向于渲染引擎，渲染引擎是一种对HTML文档进行解析并将其显示在页面上的工具

常见渲染引擎：
	gecko引擎之firefox
	IE使用Trident引擎
	opera最早使用Presto引擎，后来弃用
	chrome\safari\opera使用webkit引擎
	13年chrome和opera开始使用Blink引擎

JS引擎：
	老版本IE使用Jscript引擎
	E9之后使用Chakra引擎
	edge浏览器仍然使用Chakra引擎
	firefox使用monkey系列引擎
	safari使用的SquirrelFish系列引擎
	Opera使用Carakan引擎
	chrome使用V8引擎。nodeJs其实就是封装了V8引擎
