---
layout: post
title: 利用JS输出ul下li的index索引值
categories: 前端技术
description: some word here
keywords: JavaScript
tags: 
cover: https://likonion-1254082995.cos.ap-chengdu.myqcloud.com/media/logoOg.png
---

原生JavaScript实现index

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <title>打印索引</title>
    </head>
    <style type="text/css">
        li{background: pink;margin-bottom: 10px;height: 40px;}
    </style>
    <body>
        <ul>
            <li>click</li>
            <li>click</li>
            <li>click</li>
            <li>click</li>
            <li>click</li>
        </ul>
    </body>
    <script>
            var nodeList = document.getElementsByTagName('li');
        for (var i = 0; i < nodeList.length; i++) {
           nodeList[i].addEventListener("click", function(e) {
                console.log(i)
            }, false);
        }
        </script>
</html>
```

上面代码会和我们预期的结果不应，每次点击输出的都是最后一个元素的索引。 

function(e){}的匿名函数，在执行时，每一个都会创建一个新的作用域。这些匿名函数不会立即执行，仅仅是被定义，只有在点击时才执行，被当做参数传入addEventListener函数。所以在执行时i的值已经变为最后一个索引值。

## 要解决这个问题，我们可以用以下几种方案：
### 方案1-使用闭包
```js
    var nodeList = document.getElementsByTagName('li');
       for (var i = 0; i < nodeList.length; i++) {
        (function(j){
            nodeList[j].addEventListener("click", function(e) {
               console.log(j);
            }, false);
        })(i) ;
    }
```
### 方案2-使用es6中let的块级作用域
```js
    var nodeList = document.getElementsByTagName('li');
    for (let i = 0; i < nodeList.length; i++) {
       nodeList[i].addEventListener("click", function(e) {
            console.log(i)
        }, false);
    }
```

### 方案3-利用事件委托机制
```js
    var nodeList = document.getElementsByTagName('li');
    arrNodes = Array.prototype.slice.call(nodeList);
    nodeUls = document.getElementsByTagName('ul');
    nodeUls[0].addEventListener("click",function(event){
        var event = event || window.event;
        var target = event.target || event.srcElement;
        console.log(arrNodes.indexOf(target))
    },false);
```

### 方案4-使用虚拟属性
```js
    var nodeList = document.getElementsByTagName('li');
    for(var i=0; i< nodeList.length; i++){
        nodeList[i].index = i;
        nodeList[i].addEventListener("click",function(e){
            console.log(this.index)
        },false)
    }
```

### 方案5-利用自定义属性

`ul>li`结构中给li标签加上自定义属性

```html
<ul>
    <li data-index="0">click</li>
    <li data-index="1">click</li>
    <li data-index="2">click</li>
    <li data-index="3">click</li>
    <li data-index="4">click</li>
</ul>
````
然后js代码如下
```js
var nodeList = document.getElementsByTagName("li");
    for(var i=0; i<nodeList.length ; i++){
        nodeList[i].addEventListener("click",function(e){
            console.log(e.target.dataset.index); // 自定义属性用data-index格式的，可以使用dataset获取
            console.log(this.getAttribute("data-index"));
        },false)
    }
```



