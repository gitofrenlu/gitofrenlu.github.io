---
layout: post
title:  "h5页面在ios和android的兼容性问题"
date:   2019-01-09  +0800
categories: frontend
tag: 问题
---

* content
{:toc}


遇到的问题
===
## margin-bottom
* 背景 
```
页面上有fixed在底端的按钮  因此给div加了一个margin-bottom = button.height 防止底部按钮挡住页面上的文字
```
* 问题
```
margin-bottom 在ios上不能生效，还是不能滑动
```
* 解决
```
改为padding-bottom 
```
## 按钮点击效果：背景置灰
* 背景 
```
通过加 :active:{background-color:grey} 的方式使按钮在被点击的时候置灰
```
* 问题
```
ios上active失效
```
* 解决
```javascript
<body ontouchstart="" onmouseover=""></body>
或
document.body.addEventListener('touchstart',function(){});

```
## ios不兼容new Date('yyyy-mm-dd')
* 解决： **-** 换成 **/**

