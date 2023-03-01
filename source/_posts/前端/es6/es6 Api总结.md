---
title: es6 Api总结
date: 2020-08-20 21:34:23
categories:
- 前端
- es6
tags:
- 前端
- es6
---


# 一.

## 1.let和const

1. 在代码块内，使用`let`命令声明变量之前，该变量都是不可用的。 这在语法上，称为“暂时性死区”（temporal dead zone，简称 TDZ）。 
2. ES6 的块级作用域必须有大括号 
3. const声明一个只读的常量。一旦声明，常量的值就不能改变。只声明不赋值，就会报错。 
4. `const`声明的常量，也与`let`一样不可重复声明。 
5. `const`的作用域与`let`命令相同：只在声明所在的块级作用域内有效。 
6. `const`命令声明的常量也是不提升，同样存在暂时性死区，只能在声明的位置后面使用。 
7. 如果真的想将对象冻结，应该使用`Object.freeze`方法。 
8. const用来定义常量，一般常量使用大写
9. 声明字符串的方式，内容中可以直接出现换行

## 2.模板字符串

’${变量名}哈哈哈哈哈‘

## 3.箭头函数

1. 改变this指向
2. 不能作为构造实例化对象
3. 不能使用arguments变量
4. 箭头函数 适用与this无关的回调，数组的方法回调
5. 箭头函数不适合与this有关的回调，事件回调，对象的方法

，

## 4.允许给函数参数赋值的初始值

具有默认值的参数，一般位置都要靠后

## 5.获取实参

rest参数必须要放到最后

function fn(a,b,...args){

}

## 6.扩展运算符

【...】

能将数组转换为逗号分隔的参数序列

可以进行数组合并

可以进行数组克隆

将伪数组转为真正的数组

##7.symbol基本使用

symbol的值是唯一的。

不能与其他数据进行运算。

let s=Symbol();

let s2 = Symbol("hh");

let s3  = Symbol.for("hh")；

作用是给对象添加属性和方法:

let youxi = {

​	name:"狼人杀",

​	[Symbol('day')] function(){

​		console.log("我可以发言")

​	}

[Symbol('zibao')]:function(){

​	console.log("我可以自爆")

​	}

}

console.log(youxi)

## 8.Symol的内置属性

## 9.迭代器

> 迭代器是一种接口(inerator),为各种不同的数据结构提供统一的访问机制，任何数据结构只要部署 Iterator 接口，就可以完成便利操作。
>
> Iterator 接口指的是对象原型里的 Symbol 属性。

+ **原理**
  1. 创建一个指针对象，指向当前数据结构的起始位置。
  2. 第一次调用的 next 方法，指针自动指向数据结构的第一个成员
  3. 往后不断调用 next ，指针一直往后移动，知道最后一个成员。
  4. 每次调用 next 都会返回一个包含 value 和 dome 属性的对象。





## 10.生成器函数Generator 

> 一个函数一旦开始执行，就会运行到最后或遇到return时结束，运行期间不会有其它代码能够打断它，也不能从外部再传入值到函数体内，而 Generator  函数（生成器）可以使用 yield 暂停执行，使用 next 可继续执行。 
>
> Generator 不能写成箭头函数形式

+ **语法**

  ```js
   function *genFun(){
       代码1
       yield 代码; // 遇到 yield 后暂时不执行，需要 next() 后才执行。
   	 代码2
   }
       
       
   let genObj = genFun();
   genObj.next() // generator 函数需要使用 next() 执行
  ```

+ **yield**

  ```js
  1.传参
  function *genFun(nub){
      代码1
      let a = yield // 接第二次 next(2) 时的参
  	代码2
      return 4
   }
  
  
  let genObj = genFun(3)
  let res1 = genObj.next(1) // 这里没办法传给 yield
   res1 // { value:2,done:false } false 表示没有执行完毕
  let res2 = genObj.next(2) // 传给 yield 
   res2 // { value:4,done:true } true 表示执行完毕,它的 value 依靠的是 return
  ```

+ **runner()**

  ```js
  runner(function *(){
      let data1 = yield ajax
      let data2 = yield ajax
      let data3 = yield ajax
      console.log(data1,data2,data3)
  })
  ```

  

## Promise

> Promise 就是用来管理异步操作的，使异步操作没有多层嵌套，就像同步操作一样

### promise的状态

+ pending 待解决,等待
+ fulfilled 成功
+ rejected 失败

```js
const p = new Promise((resolve,reject) => {
	resolve() //可以将状态改为 fulfilled
    reject() //可以使当前状态改为 rejected
    # promise的状态是一次性的，不可同时调用。
})

console.log(p) 
```

### Promise的结果

```js
const p = new Promise((resolve,reject) => {
	resolve('成功的结果')  //传递参数，改变当前promise对象的结果
    reject('失败的结果')  // 失败的结果
})
console.log(p) 
```

### Promise的方法



> promise原型中的方法

+ **1.then() **

  ```js
  p.then((value)=>{
      value resolve 的参数
      状态是 fulfilled 时的调用
  },(err)=>{
      err reject 的参数
      状态是 rejected 时的调用,相当于catch()
  }) 
  
  then会返回一个新的promise，状态是 pending ,从而实现一个链式操作。
  promise的状态不改变时不会执行then。
  在then的成功回调函数中使用 return 可以将返回的 Promise 的状态改为 fulfilled ，如果代码出错则 rejected 。
  ```


+ **2.catch()**

+ ```js
1.当 promise 的状态为 rejected 时被执行
  2.当 promise 执行体中出现代码错误执行
  p.catch((reason)=>{
      
  })
  ```
  
+ **3.finally()**

  ```js
  不管状态如何都会执行的操作
  p.finally() 不接受任何参数，返回原来的值,这意味着没有办法知道，前面的 Promise 状态到底是fulfilled还是rejected。这表明，finally方法里面的操作，应该是与状态无关的，不依赖于 Promise 的执行结果。
  ```

+ **all()**

  ```js
  将多个 Promise 实例，包装成一个新的 Promise 实例，所有的都成功 p 返回的 Promise 的状态才会为 fulfillted,否则为 rejected
  
  p = Promise.all([p1,p2,p3]) 参数是数组，返回的也是一个数组，数组的每一项是每一个请求对应的结果。
  ```

+ **race()**

  ```js
  将多个 Promise 实例，包装成一个新的 Promise 实例，只要有一个实例率先改变状态 Promise 的状态就跟着改变,率先改变的 Promise 实例返回值就传递给p的回调函数
  
  p = Promise.race([p1,p2,p3]) 
  ```

+ **allSettled()**

    ```js
  接受一组 Promise 实例作为参数，包装成一个新的 Promise 实例，只有等到所有这些参数实例都返回结果，不管是 fulfilled 还是 rejected 包装实例才会结束。
  ```

+ **any()**

  ```js
  接受一组 Promise 实例作为参数，包装成一个新的 Promise 实例，只要有一个返回 fulfilled 状态，包装实例就会变成 fulfilled 如果所有的参数实例都变成 rejected 状态，包装实例就会变成 rejected 状态。
  ```

+ **resolve()**

  ```js
  将现有对象转为 Promise 对象
  
  Promise.resolve(参数)
  
  参数情况：
  1.参数是 Promise 实例
    不做任何修改，原封不动的返回这个实例
    
  2.参数是有then方法的对象
    转为 Promise 对象，并立即执行 then() 方法。
   
  3.不具有 then() 或者 不是对象
    转为 Promise 对象，状态为 resolved。
    
  4.不带有任何参数
    直接返回 resolved 状态的 Promise 对象。
  ```

+ **reject()**

  ```js
  返回一个新的 Promise 实例，该实例状态为 rejected
  
  const p = Promise.reject('出错了')
  p.then(null,function(){
      出错
  })
  
  它的参数会原封不动的作为 reject 的理由，变成后续方法的参数。
  ```

  







## 12.set

let s = new Set();

let s2 = new Set(['1','2'])

## 13.Map

let m = new Map()

m.set('name','哈哈')

## 14.class类

class 类名{

​	constructor(){

​		

​	}

}

## 15.数组扩展

对象方法扩展

## 16.模块化

防止命名冲突，代码复用，搞维护性

export  

import  

暴露有三种语法

解构赋值形式



使用npm下载包来导入暴露模块化



# ES7新特性

includes 方法查文档

# ES8新特性



async 和 await

async函数：

返回的结果是Promise的对象，对象的状态由函数内部的return决定

await表达式：要放在async函数中，async中可以没有await



对象方法的扩展

获取所有对象值

Object.keys()

Obgect.entries()

更多请查文档

# ES9新特性

对象的合并

正则扩展命名捕获分组

反向断言



# ES10

##1.Object.fromEntries 用来创建对象数组

##2.字符串的扩展方法

str.trimStart()：清除尾部空白

str.trimEnd()：清除头部空白

##3.数组方法：

flat与 flatMap

##4.用来获取Symbol的字符

Symbol.prototype.description

let s = Symbol('1')

s.description 

## 5.私有属性

class Person{

name:

/#age

}

可用方法访问

info(){

​	this.#age

}

## 6.Promise.allSettled

返回的结果始终是成功的，根据返回结果来顶

all() 会报错



## 7.matchAll

用来数据的批量提取

 ## 8.可选链接符

?.判断当问号前面的属性存在的话执行.号后属性



# ES11



## 1.动态import

import('111.txt').then(module =>{module.111()})

## 2.大整形

let n = 134

BigInt(n)

用于大数值运算



## 3.globalThis.js

全局对象，操作全局对象





