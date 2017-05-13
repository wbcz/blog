---
title: stream
type: "categories"
categories: 后端
---

## 先来普及一下概念

### 什么是流IO？

> 流是一种抽象概念，它代表了数据的无 结构化传递。 按照流的方式进行输入输出，**数据被当成无结构的字节序或字符序列**。从流中取得数据的操作称为提取操作，而 向流中添加数据的操作称为插入操作。用来进行输入输出操作的流就称为IO流。换句话说，**IO流就是以流的方式进行输入输出。输入输出（IO）是指计算机同 任何外部设备之间的数据传递**。常见的输入输出设备有文件、键盘、打印机、屏幕等。数据可以按记录（或称数据块）的方式传递，也可以流的方式传递。


> 流(stream)的概念源于UNIX中管道(pipe)的概念。在UNIX中，**管道是一条不间断的字节流，用来实现程序或进程间的通信**，或读写外围设备、外部文件等。一 个流，必有源端和目的端，它们可以是计算机内存的某些区域，也可以是磁盘文件，甚至可以是Internet上的某个URL。流的方向是重要的，根据的方 向，流可分为两类：输入流和输出流。
java中将输入输出抽象称为流，就好像水管，将两个容器连接起来。将数据冲外存中读取到内存流中的称为输入流，将数据从内存写入外存中的称为输出流。读==》输入，写==》输出


### 数据流概念

> 是处理系统缓存的一种方式。操作系统采用数据块（chunk)的方式读取数据，每接收一次数据，就存入缓存。

#### 处理缓存的两种方式
> 数据全部接收，读入内存，一次性读取
> 边读边接收

#### 特点
> 通过事件通信，具有readable、writable、drain、data、end、close等事件，既可以读取数据，也可以写入数据。
读写数据时，每读入（或写入）一段数据，就会触发一次data事件，全部读取（或写入）完毕，触发end事件。如果发生错误，则触发error事件

### Stream接口三类
> 可读数据流接口，用于对外提供数据。
> 可写数据流接口，用于写入数据。
> 双向数据流接口，用于读取和写入数据，比如Node的tcp sockets、zlib、crypto都部署了这个接口。

#### 哪些对象部署了Stream接口
> 文件读写
> HTTP请求读写
> TCP的连接
> 标准的输入输出

### 可读数据流
> 数据的来源
```
var Readable = require('stream').Readable;

var rs = new Readable();
rs.push('beep ');
rs.push('boop\n');
rs.push(null);

rs.pipe(process.stdout);
//可读数据流的push方法，用来将数据输入缓存
//rs.push(null)中的null，用来告诉rs，数据输入完毕。
```

#### 可读数据流状态
> 流动态和暂停态，流动态的时候才能读取数据，如果处于暂停态，需要显示调用stream.read(),转换成流动态

#### 暂停转流动态

> 添加data事件的监听函数
> 添加resume方法
> 调用pipe方法将数据送往一个可写的数据流

#### 流动转为暂停态
> 不存在pipe方法的目的地时，调用pause方法
> 存在pipe方法的目的地时，移除所有data事件的监听函数，并且调用uppipe方法，移除目的地

注意，只移除data事件的监听函数，并不会自动引发数据流进入“暂停态”。
另外，存在pipe方法的目的地时，调用pause方法，并不能保证数据流总是处于暂停态，一旦那些目的地发出数据请求，数据流有可能会继续提供数据。
每当系统有新的数据，该接口可以监听到data事件，从而回调函数

```
var fs = require('fs');
var readableStream = fs.createReadStream('file.txt');
var data = '';

readableStream.setEncoding('utf8');

readableStream.on('data', function(chunk) {
  data+=chunk;
});

readableStream.on('end', function() {
  console.log(data);
});
```
上面代码中，fs模块的createReadStream方法，是部署了Stream接口的文件读取方法。该方法对指定的文件，返回一个对象。该对象只要监听data事件，回调函数就能读到数据。

除了data事件，监听readable事件，也可以读到数据。

```
var fs = require('fs');
var readableStream = fs.createReadStream('file.txt');
var data = '';
var chunk;

readableStream.setEncoding('utf8');

readableStream.on('readable', function() {
  while ((chunk=readableStream.read()) !== null) {
    data += chunk;
  }
});

readableStream.on('end', function() {
  console.log(data)
});
```
readable事件表示系统缓冲之中有可读的数据，使用read方法去读出数据。如果没有数据可读，read方法会返回null。

> Readable.pause() ：暂停数据流。已经存在的数据，也不再触发data事件，数据将保留在缓存之中，此时的数据流称为静态数据流。如果对静态数据流再次调用pause方法，数据流将重新开始流动，但是缓存中现有的数据，不会再触发data事件。
> Readable.resume()：恢复暂停的数据流。
> Readable.unpipe()：从管道中移除目的地数据流。如果该方法使用时带有参数，会阻止“可读数据流”进入某个特定的目的地数据流。如果使用时不带有参数，则会移除所有的目的地数据流。

### 总结
> 想要读取数据，需要实现stream的接口
> 读取数据的流程是先把数据读到缓存，并且让可读数据流是流动态
> 用事件来监听并且导入数据
