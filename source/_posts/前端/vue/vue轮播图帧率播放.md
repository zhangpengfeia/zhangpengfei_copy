---
title: vue实现图片帧率轮播播放
date: 2020.09.25
categories:
- 前端
- vue
tags:
- 前端
- vue
---

[TOC]

## 需求
1. 传入一个数组，数组中放的是目录名称，通过本目录名称，读取文件目录下的所有图片，并循环播放，形成一个每1s播放多少张图片的效果，最后目录播放完毕后，在跳到第一个目录循环播放。
2. 核心: 用 webpack的一个API ==require.contex==读取目录下的文件名,具体想了解的可以查一查。

## 代码
+ HTML
```html
<template>
  <div id="imgPlay" ref="container" :style="[style]">
    <img :src="imgsrc" :style="[{height:style.height,width:style.width}]">
    <div id="but">
      <button @click="start()">开始</button>
      <button @click="stop()">停止</button>
    </div>
  </div>
</template>
```
+ javascript
```javascript
<script>
export default {
  name: 'ZxImgPlay',
  data () {
    return {
      style:[
		width:"50px",
		height:"50px"
		],
      interval: null, // 定时器id
      flag: true, // 定时器的开关
      setIntervalNumber: 0, // 当前展示的图片下标
      imgsrc: "", // 要展示的图片路径
      imgUrls: [], // 所有的图片路径
      frameRate: 0 // 帧率
    }
  },
  computed: {},
  watch: {},
  created () { },
  mounted () {
    this.zxInit()
  },
  beforeDestroy () { },

  methods: {
    zxInit () {
    // 这里的 this.DisplayParam 是公司内部的一套东西，混入的对象
    // this.DisplayParam.frameRate 是一个数组["目录名1","目录名2"]
    // this.DisplayParam.imgUrls 是死图当没有目录的时候就用死图
    // this.DisplayParam.frameRate 是传入的帧率
      this.frameRate = this.DisplayParam.frameRate && (1000 / this.DisplayParam.frameRate)
      this.imgUrls = this.DisplayParam.imgUrls
      this.DisplayParam.imageFileName != 0 ? this.readdir(this.DisplayParam.imageFileName) : this.showImages(true)
    },

    start () {
      if (this.flag) return
      this.showImages()
      this.flag = true
    },

    stop () {
      this.flag = false
      clearInterval(this.interval)
    },

    readImages (imageFileName, _A) {
      this.stop()
      let req = require.context("@/../static/images", true, /\.(?:bmp|jpg|gif|jpeg|png)$/).keys();
      let path = new RegExp(imageFileName[_A])
      req.forEach(item => {
        if (path.test(item)) {
          this.imgUrls.push({ img: "@/../static/images/" + imageFileName[_A] + item.substring(item.lastIndexOf('/')) })
        }
      })
      this.showImages()
    },
    readdir (imageFileName) {
      this.imgUrls = []
      for (let i = 0; i < imageFileName.length; i++) {
        this.readImages(imageFileName, i)
      }
    },

    showImages (_B) {
      if (_B) this.imgUrls = this.imgUrlsSort(this.imgUrls, 'sort')
      console.log(this.imgUrls)
      this.interval = setInterval(this.setIntervalFun, this.frameRate)
    },

    imgUrlsSort (ary, key) {
      return ary.sort((a, b) => {
        let x = a[key];
        let y = b[key];
        return ((x < y) ? -1 : (x > y) ? 1 : 0)
      })
    },

    setIntervalFun () {
      if (this.setIntervalNumber >= this.imgUrls.length) {
        this.setIntervalNumber = 0
      }
      this.imgsrc = this.imgUrls[this.setIntervalNumber++].img || ''
    }
  }
}
</script>
```
## 问题
上述这么做已经实现了功能，但是目前来说是发现了两个问题
1. require.context() 这个API它的第一个参数不能用一个可变的值，比如变量，会有警告。
2. 上述代码一直更换图片的src实现的，也就是说每次换src时都会发送http请求获取图片,导致了内存不会被释放一直增加。

