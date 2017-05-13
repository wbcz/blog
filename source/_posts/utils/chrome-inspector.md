---
title: 用PC的chrome浏览器调试手机的Safari页面
type: "categories"
categories: 工具
---

## 背景
在Mac上用safari调试手机页面，有个弊端，就是第一次的console.log和其他的记录是捕捉不到的，而且习惯用chrome调试的同学对safari无感，所以 [ios-webkit-debug-proxy](https://github.com/google/ios-webkit-debug-proxy)诞生了

### 安装
> brew install ios-webkit-debug-proxy
> brew uninstall --force libimobiledevice ios-webkit-debug-proxy
> brew install --HEAD libimobiledevice ios-webkit-debug-proxy

### 连接手机
> 设置 > Safari > 高级 > Web 检查器 > 打开

### 启动代理

terminal执行

> ios_webkit_debug_proxy -f chrome-devtools://devtools/bundled/inspector.html

### pc的chrome浏览器启动

查看终端连接的设备
```
http:／／localhost:9221
```
查看可以调试的页面
```
http://localhost:9222 
```
进行调试你所指定的页面

```
chrome-devtools://devtools/bundled/inspector.html?ws=localhost:9222/devtools/page/1 //这个1代表的是localhost:9222中页面的排列序号
```

如果你要调试debug模式
```
ios_webkit_debug_proxy -f chrome-devtools://devtools/bundled/inspector.html --debug //在terminal执行

https://chrome-devtools-frontend.appspot.com/static/33.0.1722.0/devtools.html?ws=localhost:9222/devtools/page/1 //浏览器输入

```