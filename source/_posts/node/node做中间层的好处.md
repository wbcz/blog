---
title: node作中间层
type: "categories"
categories: 后端
---

# 优点
- 一个Server端可以服务于多个Client端（IOS、andriod、Node）
- node层处理业务逻辑，做高层次的抽象，后端提供相关服务，让java端和前端更加解耦
- node方便部署

# 什么时候选择node作为中间层
- SPA 项目并有服务端渲染需求的项目
- 大批量页面渲染，多为静态且页面大，少量的动态数据 

# 缺点
不适合做cpu密集型的运算，因为会阻塞