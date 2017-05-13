---
title: UMD规范
type: "categories"
categories: 后端
---

### AMD 预加载异步加载，一个文件中可定义多个模块

### CMD 执行到了再加载

### CommonJS 一个文件一个模块，同步加载，单独作用域
为什么服务端用CommonJS无妨呢， ，因为所有的模块都存放在本地硬盘，可以同步加载完成，等待时间就是硬盘的读取时间。但是，对于浏览器，这却是一个大问题，因为模块都放在服务器端，等待时间取决于网速的快慢，可能要等很长时间，浏览器处于"假死"状态。

### UMD规范


```
(function (root, factory) {
    if (typeof define === 'function' && define.amd) {
        // AMD
        define(['jquery', 'underscore'], factory);
    } else if (typeof exports === 'object') {
        //if (typeof define === 'function' && define.cmd) {} 在前端可以用这个
        // Node, CommonJS-like
        module.exports = factory(require('jquery'), require('underscore'));
    } else {
        // Browser globals (root is window)
        root.returnExports = factory(root.jQuery, root._);
    }
}(this, function ($, _) {
    //    methods
    function a(){};    //    private because it's not returned (see below)
    function b(){};    //    public because it's returned
    function c(){};    //    public because it's returned

    //    exposed public methods
    return {
        b: b,
        c: c
    }
}));

```

### 模块开发的好处：使各模块高内聚、低耦合，方便拓展维护