---
title: 关于ClientWebAPI
description: ""
date: 2025-05-22T02:48:59.985Z
preview: ""
draft: false
tags: []
categories: []
image: ""
slug: about-client-web-api
---

看到了Hono.JS的介绍，
其中有一个部分是它可以 run everywhere，因为其使用了Web API Standard。

正好自己之前对这些Web API一知半解，所以简单复习了一部分。

## DOM / BOM Manipulation

主要是利用了`document`、`window`等对象来操作DOM和BOM。
- `document`：提供操作文档的方法。例如`querySelector`、`getElementById`等。通过这种script可以让网页动态化等等。
- `window`：表示浏览器窗口的对象，可以知道比如说窗口的dimension，也可以操作例如滚动窗口到哪些地方。一些event handler也是直接绑在window上。
    - 也因此复习了一下可以监听的events，例如`scroll`。使用`addEventListener`可以绑定事件处理函数，`removeEventListener`可以解绑事件处理函数避免内存泄漏。
- `history`：表示浏览器的历史记录，可以用来操作浏览器的前进后退等功能。

## Fetch API
应该是后端接触最多的一种API了。
`Request`和`Response`是两个最重要的对象。

二者基本上都可以调用类似的方法来进行解析。
- `.formData()`：将数据解析为`FormData`对象，文件也会被自动解析为`File`对象。
- `.json()`：将JSON数据解析为JavaScript对象。
- `.text()`：将文本数据解析为字符串。
- `.arrayBuffer()`：将数据解析为`ArrayBuffer`对象。（可以用来直接处理二进制数据）

除此之外`Response`还有`.ok`、`.status`、`.statusText`等属性可以用来判断请求是否成功。

另外一般地，如果没有特殊需求的情况下，基本上API endpoint都是用`json`或`multipart/form-data`来传输数据，比较少直接传输二进制数据或者单纯的text。

## Local Storage 和 Session Storage
存储在浏览器中的key-value pair，每一个origin都有自己的独立存储空间。
不同的是：
- `localStorage`：存储的数据在浏览器关闭后仍然存在，除非被purge。多个同origin的tab之间是共享的。
- `sessionStorage`：存储的数据在浏览器关闭或标签页关闭后会被删除。按照标签页隔离。