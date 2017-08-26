---
title: buffer
type: "categories"
categories: 后端
---

# 为什么要出现Buffer
因为js删除处理unicode数据，但对二进制数据处理不擅长，所以诞生了Buffer对象（对node.js处理二进制数据的接口）
# buffer特点
buffer所占用的内存不是通过V8来分配的，属于堆外内存，不是通过fs.readFile()和fs.writeRile()直接对大文件进行操作，而是通过fs.createReadStream()和fs.createWriteStream()方法通过流的方式对大文件进行操作
# Buffer结构
Buffer是一个像Array的对象，主要用于操作字节

## 模块结构
buffer是一个Javascript与c++结合的模块，性能部分用C++实现，非性能相关部分用Javascript实现

## Buffer对象
Buffer类似于数组，元素为16进制的两位数，即0-255的数值，utf-8下，中文占3个元素，字母和半角点占用1个

## Buffer内存分配
Node采用了slab分配机制，动态内存管理机制，以8kb作为一个界限，分配小buffer对象和大buffer对象

#Buffer的转换
支持的编码 ASCII UTF_8 Base64等

## 字符串转化成Buffer
new Buffer(str, [encoding])

## Buffer转字符串
buf.toString([encoding], [start], [end])

## Buffer不支持的编码类型
Buffer.isEncoding(encoding) 判断方法
不支持的采用第三方模块，iconv和iconv-lite，前者是采用C++调用libiconv库完成，后者是纯Javascript，因为转码耗cpu，在v8的高性能下，少了C++到Javascript的转换，所以后者更好

## buffer的拼接
宽字节的中文会造成乱码问题，比如‘大哥大’这个三个字符一共9个字节，我们要是设置，highWaterMark设置为8，那么就有一个字节将会以乱码输出

## setEncoding() 和 string_decoder()
setEncoding()是为了让data事件传递的不是一个Buffer对象，而是编码的字符串，输出的时候不再受Buffer大小影响了，因为在调用setEncoding（）时，可读流对象在内部设置了一个decorder对象，每次data事件都通过decorder对象进行Buffer到字符串的解码，然后传递给调用者。

## 正确拼接buffer
用一个数组来存储收到的所有Buffer片段，并且记录下所有片段的总长度，调用Buffer.concat()方法生成一个合并的Buffer对象，Buffer.concat()方法封装了从小buffer对象到大buffer对象的复制过程，实现十分细腻

## buffer与性能
字符串的传输效率低于buffer对象的传输效率，当读取大文件的时候，highWaterMark值的大小越大，读取速度越快