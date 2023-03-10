---
title: vue原理
date: 2020-08-20 21:34:23
categories:
- 笔记
- 零碎
tags:
- 前端
- vue
---
[TOC]

## mvc和mvvm

### mvc

```js
view(视图层)  
controller(路由，控制器)
model(后端给的JSON数据或死数据)

用户通过视图向后端发送请求，通过路由拦截，转发到对应的控制器的处理，获取到后端数据，最终渲染到页面
```

```mermaid
graph TD
A[View:视图发送请求] -->B[Controller:路由拦截转发到对应控制器]
	B --> C[Model:后端数据]
```

### mvvm

```js
Model(数据)
View(视图)
ViewModel(视图数据)


Vue就是一个典型的 mvvm 。
直接把数据挂载在vue实例中，通过vue可直接将数据渲染视图
视图发生改变，vue也会监听到视图的变化，在将视图中的数据写回数据层，
数据发生改变时，vue也会监听到数据变化，在将数据同步到视图。
称为数据的双向绑定。
```

```mermaid
graph TD
A[view:视图] -->B[viewModel:视图模型]
	B -->C[model:数据]
```



## 监听响应式数据

### 监听对象变化

> 通过数据劫持结合发布者,订阅者模式的方式来实的,通过 Object.definePropertyget劫持各个属性的getter和setter方法，get是获取属性时调用，set属性变化时调用，从而可以在里面做一些更新视图的操作，但是订阅者(watcher)是有多个的，怎么能知道那些订阅者需要更新，这时候就需要使用一个dep来收集订阅者(watcher)，进行统一管理，此外还需要一个指令解析器，对每个节点元素进行扫描和解析指令，再将对应的指令初始化并绑定相应更新(update)函数，此时订阅者接收到相应属性的变化，就会执行对应的更新函数，实现更新视图。

```js
执行的步骤大概是这样的：

1.vue实例在初始化时会拿到当前data中的数据，调用一个 initData(vm) 方法初始化数据。 
2.在 initData 方法中调用 Observer(val) 方法并传入数据，判断如果数据没有被观测过的话就是用 new Observer(value) 观测数据,观测的一种是数组，一种是对象。
3.如果是对象型非数组的话调用 this.walk(value) 方法，会把当前传入的对象进行循环调用Object.defineProperty。
4.使用 Object.defineProperty 重新定义数据,在判断如果当前对象的至还是一个对象的话，回到第2步进行递归。
5.取值时调用 Object.defineProperty 的 get() 方法收集（订阅者）watcher。
6.如果数据改变时调用set()，通知（订阅者）watcher去更新数据,如果当前值和数据的值不一样的话调用 dep.notify() 通知视图更新。

	
** 基础类型的数据是不会进行观测，只监测data对象
```

### 监听数组的变化

> Object.defineProperty是监听不到数组的变化的，而是使用函数劫持，对数组常用的7个原生可改变数组的方法进行了改写，只要数组一改变就通知观测者

```js
vue将data中的数组，进行了原型链重写，指向了自己定义的数组原型方法，这样当调用数组API时，可以通知依赖更新，如果数组内包含引用类型，会对数组中的引用类型再次进行监控。

1.vue初始化时会拿到当前data中的数据，调用 initData(vm) 方法初始化数据。 
2.在initData中调用 Observer(val) 方法，判断如果数据没有被观测过的话就是用 new Observer(value) 观测数据,再判断如果有__ob__且类型是observer的话说明已经被监听了直接返回数据，观测的一种是数组，一种是对象。
3.如果为数组，让就让它的原型链指向 arrayMethods ，来改写数组方法。
4.只要调用了数组方法，就会执行一个函数，执行原来的方法并通知视图更新，ob.dep.notify()
5.如果使用了数组新增方法,push,unshift,splice的话，调用ob.observeArray(新增的数据)遍历数组的每个对象进行深度观测，所以只有数组里的对象才能进行响应式的数据变化。
```

## 为何采用异步渲染

> vue是组件级更新，当组件数据变就会更新 ,如果不采用异步更新，那么组件内的数据每次更新都会对当前组件进行重新渲染， 所以为了性能考虑，vue在本轮数据更新后，再去异步更新视图

```js
1.当数据变化后，调用dep.notify()方法,通知watcher进行更新
2.watcher会调用 update() 方法，调用时并不会立即更新，而是调用 queueWatcher方法 判断watcher的id（uid）去重后放入 queue队列 中。
3.最后在调用 nextTick(flushScheduierQueue) 异步刷新 queue队列,执行watcher,update钩子函数

**可以说是 渲染节流
```



## key的原理

> 首先它的作用是：更高效的更新虚拟dom，提高diff算法的速度。
>
> 在vue中，我们是不需要直接操作dom的，只需要操作数据就可完成页面的渲染,vue通过虚拟dom去操作真实dom实现渲染，在渲染时使用的是diff算法，v-for更新渲染过的元素列表时会采用就地复用策略，简单复用此处每个元素。

+ 默认的diff算法,也就是不加key的话，比如有abcd4个节点，我们要在b和c之间插入一个e节点，那么当e插进去后，旧节点c的状态就会被e复用，d被c复用,这样新d永远都会在最后一个节点。

+ 加上key之后，相当于给每个节点加上一个唯一的标识，diff算法就可以正确的识别此节点，找到正确的位置插入新节点，并复用自身的状态，从而重用和重新排序已有节点。

+ **diff算法**：通过对比同一层的新旧虚拟dom,将有变化更新的地方渲染在真实dom。

+ **虚拟dom**：用js对象的形式模拟一个真实dom，如果直接操作dom的话，每次更新dom 都会造成重绘和回流，而使用虚拟dom 对比节点时放在js来做，可以避免真实dom 重复大量渲染。

  **其实，虚拟dom的子元素列表只包含文本节点且dom结构一致，不设置key效率会更高，因为不会涉及到过多的判读逻辑**