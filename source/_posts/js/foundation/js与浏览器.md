---
title: js与浏览器
type: "categories"
categories: [前端, JS, 基本概念]
---

## js为什么能在浏览器上跑起来

- 因为浏览器是一个解释器，对这门语言做出解释，执行指令，按照编译原理的步骤，经过词法分析，语法分析等等
- 但是不同的浏览器可能解析JS的引擎不一样，所以就有不用的解释出现，所以就会在不同的浏览器出现不一样的效果，存在兼容性问题
- 后来为了统一规范，制定了统一的ECMAScript标准，让各个浏览器都来遵循这个标准

##  JavaScript单线程
总所周知，JavaScript是以单线程的方式运行的。那为什么js是单线程的呢，这与它的用途有关，js用于互动和操作DOM，如果两个多线程同时控制一个DOM元素，势必会乱掉，但是如果引入锁的概念，会提高复杂性，所以js原来设计的时候就是基于单线程的。因为是单线程，在某一时间只能执行特定的任务，并且会阻塞其他任务的执行，遇到类似I/O耗时的任务就很坑，所以异步回调就出来啦，这些耗时的任务就由回调的方式处理，但是为了避免特别繁重的耗时操作（运算等）HTML5提供了一个web worker，他会基于主线程创造一个新的开辟线程来加载特定的js文件，这两个线程之间不会互相影响，这个worker类提供了与主线程数据交换的接口：postMessage和onMessage事件，但是web worker是不能操作DOM的，任何操作DOM的操作都需要委托给js主线程来处理，但任然改变不了js单线程的本质

## 并发模式与Event Loop
<img src="/images/bf.png">
单线程有**并发**注意是并发而不是并行，**所谓“并发”是指两个或两个以上的事件在同一时间间隔中发生**由于计算机系统只有一个CPU，故ABC三个程序从“微观”上是交替使用CPU，但交替时间很短，用户察觉不到，形成了“宏观”意义上的并发操作。

## Runtime 概念

<img src="/images/runtime.png">
### 栈 stack

这里放着JavaScript正在执行的任务。每个任务被称为帧（stack of frames）

```
function f(b){
  var a = 12;
  return a+b+35;
}

function g(x){
  var m = 4;
  return f(m*x);
}

g(21);

```
上述代码调用 g 时，创建栈的第一帧，该帧包含了 g 的参数和局部变量。当 g 调用 f 时，第二帧就会被创建，并且置于第一帧之上，当然，该帧也包含了 f 的参数和局部变量。当 f 返回时，其对应的帧就会出栈。同理，当 g 返回时，栈就为空了（栈的特定就是后进先出 Last-in first-out (LIFO)）

### Heap（堆）

一个用来表示内存中一大片非结构化区域的名字，对象都被分配在这。

### Queue（队列）

一个 JavaScript runtime 包含了一个任务队列，该队列是由一系列待处理的任务组成。而每个任务都有相对应的函数。当栈为空时，就会从任务队列中取出一个任务，并处理之。该处理会调用与该任务相关联的一系列函数（因此会创建一个初始栈帧）。当该任务处理完毕后，栈就会再次为空。（Queue的特点是先进先出 First-in First-out (FIFO)）。

- Stack栈为主线程
- Queue队列为任务队列（等待调度到主线程执行）

OK，上述知识点帮助我们理清了一个 JavaScript runtime (JS 引擎）的相关概念，这有助于接下来的分析。

### Event Loop

“任务队列”是一个事件的队列，如果I/O设备完成任务或用户触发事件（该事件指定了回调函数），那么相关事件处理函数就会进入“任务队列”，当主线程空闲时，就会调度“任务队列”里第一个待处理任务，（FIFO）。当然，对于定时器，当到达其指定时间时，才会把相应任务插到“任务队列”尾部。

#### “执行至完成”

每当某个任务执行完后，其它任务才会被执行。也就是说，当一个函数运行时，它不能被取代且会在其它代码运行前先完成。
当然，这也是Event Loop的一个缺点：当一个任务完成时间过长，那么应用就不能及时处理用户的交互（如点击事件），甚至导致该应用奔溃。一个比较好解决方案是：将任务完成时间缩短，或者尽可能将一个任务分成多个任务执行。

#### 绝不阻塞
JavaScript与其它语言不同，其Event Loop的一个特性是永不阻塞。I/O操作通常是通过事件和回调函数处理。所以，当应用等待 indexedDB 或 XHR 异步请求返回时，其仍能处理其它操作（如用户输入）

## 深入了解定时器

零延迟 setTimeout(func, 0)

零延迟并不是意味着回调函数立刻执行。它取决于主线程当前是否空闲与“任务队列”里其前面正在等待的任务。
```
(function () {

  console.log('this is the start');

  setTimeout(function cb() {
    console.log('this is a msg from call back');
  });

  console.log('this is just a message');

  setTimeout(function cb1() {
    console.log('this is a msg from call back1');
  }, 0);

  console.log('this is the  end');

})();

// 输出如下：
this is the start
this is just a message
this is the end
undefined // 立即调用函数的返回值
this is a msg from callback
this is a msg from a callback1
```
setTimeout(func, 0)的起了改变执行顺序的作用

### 正版与翻版setInterval的区别
```
// 利用setTimeout模仿setInterval
setTimeout(function(){
    /* 执行一些操作. */
    setTimeout(arguments.callee, 1000);
}, 1000);

setInterval(function(){
    /* 执行一些操作 */
}, 1000);

```
可能你认为这没什么区别。的确，当回调函数里的操作耗时很短时，并不能看出它们有什么区别。其实：上面案例中的 setTimeout 总是会在其回调函数执行后延迟 1000ms（或者更多，但不可能少）再次执行回调函数，从而实现setInterval的效果，而 setInterval 总是 1000ms 执行一次，而不管它的回调函数执行多久。

所以，如果 setInterval 的回调函数执行时间比你指定的间隔时间相等或者更长，那么其回调函数会连在一起执行。

```
var counter = 0;
var initTime = new Date().getTime();
var timer = setInterval(function(){
    if(counter===2){
        clearInterval(timer);
    }
    if(counter === 0){
        for(var i = 0; i < 1990000000; i++){
            ;
        }
    }

    console.log("第"+counter+"次：" + (new Date().getTime() - initTime) + " ms");

    counter++;
},1000);
```
Chrome浏览器的输入如下：
```
第0次：2007 ms
第1次：2013 ms
第2次：3008 ms
```
从上面的执行结果可看出，第一次和第二次执行间隔很短（不足1000ms）。


## 浏览器

**浏览器不是单线程的**

上面说了这么多关于 JavaScript 是单线程的，下面说说其宿主环境——浏览器。

浏览器的内核是多线程的，它们在内核制控下相互配合以保持同步，一个浏览器至少实现三个常驻线程：
- JavaScript引擎线程：JavaScript引擎是基于事件驱动单线程执行的，JavaScript 引擎一直等待着任务队列中任务的到来，然后加以处理。
- GUI 渲染线程：GUI渲染线程负责渲染浏览器界面，当界面需要重绘（Repaint）或由于某种操作引发回流(reflow)时，该线程就会执行。但需要注意 GUI 渲染线程与 JavaScript 引擎是互斥的，当 JavaScript 引擎执行时 GUI 线程会被挂起，GUI 更新会被保存在一个队列中等到 JavaScript 引擎空闲时立即被执行。
- 浏览器事件触发线程：事件触发线程，当一个事件被触发时该线程会把事件添加到“任务队列”的队尾，等待JavaScript引擎的处理。这些事件可来自 JavaScript 引擎当前执行的代码块如setTimeOut、也可来自浏览器内核的其他线程如鼠标点击、AJAX异步请求等，但由于 JavaScript 是单线程执行的，所有这些事件都得排队等待 JavaScript 引擎处理。

在 Chrome 浏览器中，为了防止因一个标签页奔溃而影响整个浏览器，其每个标签页都是一个进程（Renderer Process）。当然，对于同一域名下的标签页是能够相互通讯的，具体可看 浏览器跨标签通讯。在 Chrome 设计中存在很多的进程，并利用进程间通讯来完成它们之间的同步，因此这也是 Chrome 快速的法宝之一。对于 Ajax 的请求也需要特殊线程来执行，当需要发送一个 Ajax 请求时，浏览器会开辟一个新的线程来执行HTTP的请求，它并不会阻塞 JavaScript 线程的执行，当 HTTP 请求状态变更时，相应事件会被作为回调放入到“任务队列”中等待被执行。

```
document.onclick = function(){
    console.log("click")
}

for(var i = 0; i< 100000000; i++);

```
解释一下代码：首先向 document 注册了一个 click 事件，然后就执行了一段耗时的 for 循环，在这段 for 循环结束前，你可以尝试点击页面。当耗时操作结束后，console 控制台就会输出之前点击事件的 "click" 语句。这证明了点击事件（也包括其它各种事件）是由额外单独的线程触发的，事件触发后就会将回调函数放进了“任务队列”的末尾，等待着 JavaScript 主线程的执行。

## 总结

- JavaScript是单线程的，同一时刻只能执行特定的任务。而浏览器是多线程的
- 异步任务（各种浏览器事件、定时器等）都是先添加到“任务队列”（定时器则到达其指定参数时）。当Stack栈（JavaScript 线程）为空时，就会读取 Queue 队列（任务队列）的第一个任务（队首），然后执行。

## 内存泄露
[参考](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/A_re-introduction_to_JavaScript#内存泄露)


