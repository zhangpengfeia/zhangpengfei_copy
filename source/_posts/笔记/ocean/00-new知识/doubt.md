---
title: js一些问题
date: 2019-05-06 14:02:48
categories:
- 笔记
- 零碎
tags:
- 前端
- 笔记
---

[TOC]



## attribute和property的区别?

自带的 attribute已经附加在property对象上了所以property中包含了自带的attribute，可以通过 dom节点.id访问。

而自定attribute不会附加到property,会在property子集中attributes中找到，可使用 setAttribute()来设置任何属性和属性值，使用getAttribute()来获取任何属性



## DOM和BOM?

DOM的跟对象是：document，DOM就是html和xml的一套api

BOM的跟对象是：window中包含document,location，等

所以BOM包含DOM



## for in 和for of的区别

for in 遍历键名，一般用于遍历对象

for of 是ES6新特新，遍历键值，一般用于遍历数组



## 块域和局部作用域的区别

块域是{} ,作用域是全局和局部作用域。

块级作用域一般是使用let和const时创建的，就是指使用let和const声明的变量只在当前块域生效。

在局部作用域中不管用var还是let都是只能在当前作用域生效



## Object.prototype.toString和toString()有关系吗？

toString()其实是按照原型链去找到 Object.prototype 上的。

Object.prototype.toString() 方法是严格函数 。

Object.prototype.toString.call()与toString.call()是真正意义上的等价 







