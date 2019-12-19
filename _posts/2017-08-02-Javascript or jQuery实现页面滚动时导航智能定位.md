---
layout: post
title: Javascript or jQuery实现页面滚动时导航智能定位
categories: 前端技术
description: Javascript or jQuery实现页面滚动时导航智能定位
tags: JavaScript jQuery
cover: https://likonion-1254082995.cos.ap-chengdu.myqcloud.com/media/20160628110530781.jpg
---

本篇文章主要介绍了Javascript或者jQuery实现页面滚动时导航智能定位，具有一定的参考价值，感兴趣的小伙伴们可以参考一下

常见的开发页面中可能会有这么一个需求，页面中会有多个模块，每个模块对应一个导航，当页面滚动到某个模块时，对应的模块导航需要加上一个类用于区分当前用户所浏览区域。

## 假设结构如下：

```html
<div class="container">
  <div class="wrapper">
    <div class="section" id="section1">section1</div>
    <div class="section" id="section2">section2</div>
    <div class="section" id="section3">section3</div>
    <div class="section" id="section4">section4</div>
    <div class="section" id="section5">section5</div>
  </div>
  <nav>
    <a href="#section1" rel="external nofollow" class="current">section1</a>
    <a href="#section2" rel="external nofollow" >section2</a>
    <a href="#section3" rel="external nofollow" >section3</a>
    <a href="#section4" rel="external nofollow" >section4</a>
    <a href="#section5" rel="external nofollow" >section5</a>
  </nav>
</div>
```

## 页面滚动时导航定位

### jQuery 实现方式：

```js
var $navs = $('nav a'),          // 导航
  $sections = $('.section'),       // 模块
  $window = $(window),
  navLength = $navs.length - 1;
  
$window.on('scroll', function() {
  var scrollTop = $window.scrollTop(),
    len = navLength;

  for (; len > -1; len--) {
    var that = $sections.eq(len);
    if (scrollTop >= that.offset().top) {
       $navs.removeClass('current').eq(len).addClass('current');
       break;
    }
  }
  ```
  
###   JavaScript 实现方式：
  
  ```js
  var $navs = document.getElementById('content-side').querySelectorAll('a'),          // 导航
    $sections = document.querySelector('.left').querySelectorAll('h1,h2,h3'),       // 模块
    $window = window,
    navLength = $navs.length - 1;
$window.addEventListener('scroll', function () {
    var scrollTop = (document.documentElement && document.documentElement.scrollTop) || document.body.scrollTop,
        len = navLength;
    function getOffset (el) {
        const box = el.getBoundingClientRect();
        return {
            top: box.top + window.pageYOffset - document.documentElement.clientTop,
            left: box.left + window.pageXOffset - document.documentElement.clientLeft
        }
    }
    for (; len > -1; len--) {
        var that = $sections[len];
        if (scrollTop >= getOffset(that).top) {
            $navs.forEach(function(e){
                e.classList.remove('current')
            })
            $navs[len].classList.add('current')
            break;
        }
    }
})
  ```
  
  


