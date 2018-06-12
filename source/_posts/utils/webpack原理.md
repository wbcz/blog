---
title: webpack原理
type: "categories"
categories: 工具
---

# 加载过程
1. 从入口文件开始递归建立一个依赖的关系树
2. 所有文件都转化成模块函数
3. 根据依赖关系，按照配置文件把模块分组打包成若干个bundle
4. 通过script标签把打包出的bundle注入到html中，通过manifest文件管理bundle文件的运行和加载

打包的规则为：一个入口文件对应一个bundle。该bundle包括入口文件模块和其依赖的模块。按需加载的模块或需单独加载的模块则分开打包成其他的bundle。
