---
title: 正则-01
date: 2019-04-02 19:41:48
categories:
- 笔记
- 正则
tags:
- 前端
- 正则
---



[TOC]

# 正则

> 依赖于一个语言环境，比原生的 jsApi 更灵活,专门来验证特定字符串，或取一些内容，正则表达式是一个复杂数据类型。

### 创建

1. __字面量创建__

   ```javascript
   // 声明的为正则
   test() 检测一个字符串是否匹配某个模式,返回的是布尔值
   exec() 获取符合规则的字符串,没有为null
   
   let str = 'houddd'
   reg.test(str)
   reg.exec(str)
   ```

   + __缺点__
     1. 不可放在 __/ /__ 中放变量，可在 __eval()__ 方法中用 __${变量名}__解决。

2. __new创建__

   ```javascript
   let reg = new RegExp('u','g')
   reg.test(str)
   ```

### 选择符

```js
1. /u|a/  | 或者
```

### 元字表或元字组

```js
1. () 一个整体
	
2. [] 
	一个整体，里面自带 | 功能
	[abcd]  = a|b|c|d

3. | 
	或 (abc|dfe) 

4. [^] 
    取反，表示不是里面的任意一个就可以 [^abcd]
	当 . 出现在 [.] 中，它表示单纯的点文本

5. (?:) 
	匹配一个整体但不捕获

6. - 	
    表示从那一个字符到哪一个字符，前提是ASCII码值需要连接 [a-z]

```

### 转义

```js
1. \ 为转义
2. \\ 双转义
```

### 字符边界

```js
1. ^ 开始
2. $ 结束
```

### 元字符

1. /d 匹配数字
2. /D 匹配非数字
3. /s 匹配空白
4. /S 非空格
5. /w 字母数字下划线
6. /W 除了字母数字下划线
7. .  除了换行之外的任何字符
8.  /t 匹配一个制表符，一个tab就是一个制表符
9. s　视为单行匹配
10. g 全局匹配
11. i 忽略大小写
12. y 粘性全局（每一次捕获的索引必须是连贯的）
13. +表示出现1--n次
14. *表示出现0--n次
15. ？表示出现0--1次
16. {n} 出现指定次数
17. {n,x} 从 n -- x 次
18.  {2,} 从 2 -- n次

### 贪婪和非贪婪

> 使用限定元字符时
>
> 贪婪：会捕获到最大长度
>
> 非贪婪：按照最小值捕获，在表达式后面加？

### 正则预查

+ 正向肯定预查

  ```js
  尽管前面相同，捕获后面必须写着指定的字符，而且还不需要指定的字符。
  如：
  	let reg = /ES(?=2015|2016)/
      console.log(reg.exec('ES2015')) // 只要ES 没有后面的部分
  ```

+ 正向否定预查

  ```js
  和正向相反，后面匹配的是不要的字符。
  	let reg = /ES(?!2015|2016)/
      console.log(reg.exec('ES2015')) // 只要ES 没有后面的部分
  ```

+ 负向肯定预查

  ```js
  前面必须按照指定字符匹配，但是不需要，只要后面的字符
  	let reg = /(?<=2015|2016)ES/
      console.log(reg.exec('2015ES')) // 只要ES 没有前面的部分
  ```

+ 负向否定预查

  ```js
  和负向肯定预查相反,前面除了指定的字符其它都可以匹配
  	let reg = /(?<!2015|2016)ES/
      console.log(reg.exec('2015ES')) // 只要ES 没有前面的部分
  ```



### 重复出现

```js
/num ：是一个正整数字，表示必须重复出现几次第一个可__被捕获__的 () 的内容
如：
	let reg = /([abcd])\d+/1/
    reg.test('a111a') //true
	reg.test('a111b') // true
	
```



### 字符串方法配合正则

```js
1. replace(正则,要替换的内容)
2. search(正则) // 如果匹配到则返回索引
3. match(正则) // 查找到字符串内一个满足字符串片段的内容返回，和 exec 一样，如果是全局 g ,那么返回的是一个数组
```

### 正则匹配中文

```js
\u 表示匹配中文，后面带上中文的 unicode 编码
[\u4e00-\u9fa5]：表示任意一个中文字符
```

