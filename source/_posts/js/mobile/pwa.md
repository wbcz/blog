---
title: PWA
type: "categories"
categories: [前端, H5]
---

#  pwa是什么？

- 谷歌推出的新一代渐进式app，何为渐进增强，即在旧的浏览器的任何功能，在支持pwa的浏览器上支持采用新的特性，享受更好的体验！
pwa需要满足三个条件:
    1.https域名
    2.manifest.json文件的配置[MDN](https://developer.mozilla.org/zh-CN/docs/Web/Manifest)
    3.server-worker[文档](https://developers.google.com/web/fundamentals/codelabs/your-first-pwapp/?hl=zh-cn)

# 应用案例
[筷马热食](https://kmrs-h5.faas.alpha.elenet.me/#/)
[饿了么App](https://h5.ele.me/msite/)

# 浏览器支持
safari最新版
chrome最新版
UC最新版

# 调试以及测试性能工具
调试(chrome调试工具下的application)
chrome拓展应用程序 lighthouse

# 如何引入到项目中
1.  webpack插件（sw-precache-webpack-plugin、html-webpack-plugin做个配置项serviceWorkerLoader）
2.  manifest.json 在文档的头部进行引入
3.  不同的环境进行不同的配置
4.  模板变量的替换

[友情链接](https://segmentfault.com/a/1190000008880637)
