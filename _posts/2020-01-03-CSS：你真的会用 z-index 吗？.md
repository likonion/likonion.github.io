---
layout: post
title: CSS：你真的会用 z-index 吗？
categories: 前端技术
description: some word here
tags: z-index
cover: https://likonion-1254082995.cos.ap-chengdu.myqcloud.com/media/x-y-z-axis1.png
---
* content
{:toc}

## HTML 元素是处于三维空间中

所有的盒模型元素都处于三维坐标系中，除了我们常用的横坐标和纵坐标，盒模型元素还可以沿着“z 轴”层叠摆放，当他们相互覆盖时，z 轴顺序就变得十分重要。

但“z 轴”顺序，不完全由 `z-index` 决定，在层叠比较复杂的 HTML 元素上使用 `z-index` 时，结果可能让人觉得困惑，甚至不可思议。这是由复杂的元素排布规则导致的。

![](https://likonion-1254082995.cos.ap-chengdu.myqcloud.com/media/16b0300df37b3bda)

### 不含 z-index 元素如何堆叠？

当没有元素包含 `z-index` 属性时，元素按照如下顺序堆叠（从底到顶顺序）：

1. 根元素（`<html>`）的背景和边界；
2. 位于普通流中的后代“无定位块级元素”，按它们在HTML中的出现顺序堆叠；
3. 后代中的“定位元素”，按它们在HTML中的出现顺序堆叠；

**注意：普通流中的“无定位块级元素”始终先于“定位元素”渲染，并出现在“定位元素”下层，即便它们在HTML结构中出现的位置晚于定位元素也是如此。**

![](https://likonion-1254082995.cos.ap-chengdu.myqcloud.com/media/16b030bbb02e863b)


### float 如何影响堆叠？

对于浮动的块元素来说，层叠顺序变得有些不同。浮动块元素被放置于非定位块元素与定位块元素之间：

1. 根元素（<html>）的背景和边界；
2. 位于普通流中的后代“无定位块级元素”，按它们在HTML中的出现顺序堆叠；
3. 浮动块元素；<<<<
4. 位于普通流中的后代“无定位行内元素”；
5. 后代中的“定位元素”，按它们在HTML中的出现顺序堆叠；

![](https://likonion-1254082995.cos.ap-chengdu.myqcloud.com/media/16b0307946e768e6)

## 堆叠上下文（Stacking Context）

### 什么是堆叠上下文？

层叠上下文（Stacking Context）是HTML元素的三维概念，这些HTML元素在一条假想的相对于面向（电脑屏幕的）视窗或者网页的用户的z轴上延伸，HTML元素依据其自身属性按照优先级顺序占用层叠上下文的空间。

在层叠上下文中，其子元素的 z-index 值只在父级层叠上下文中有意义。子级层叠上下文被自动视为父级层叠上下文的一个独立单元。

* 层叠上下文可以包含在其他层叠上下文中，并且一起创建一个有层级的层叠上下文。
* 每个层叠上下文完全独立于它的兄弟元素：当处理层叠时只考虑子元素。
* 每个层叠上下文是自包含的：当元素的内容发生层叠后，整个该元素将会 在父层叠上下文中 按顺序进行层叠。

### 如何形成堆叠上下文？

* 根元素 (HTML)
* 定位元素（relative、absolute），并且 z-index 不为 auto；
* opacity 小于 1 时；
* transform 不为 none 时；
* z-index 不为 auto 的 flex-item；

**注：层叠上下文的层级是 HTML 元素层级的一个层级，因为只有某些元素才会创建层叠上下文。可以这样说，没有创建自己的层叠上下文的元素 将被父层叠上下文包含。**

![](https://likonion-1254082995.cos.ap-chengdu.myqcloud.com/media/16b03117461de04d)

### 堆叠上下文如何影响堆叠？

1. 元素的 background 和 borders;
2. 拥有负堆叠层级（negative stack levels）的子层叠上下文（child stacking contexts）
3. 在文档流中的（in-flow），非行内级的（non-inline-level），非定位（non-positioned）的后代元素
4. 非定位的浮动元素
5. 在文档流中的（in-flow），行内级的（inline-level），非定位（non-positioned）的后代元素，包括行内块级元素（inline blocks）和行内表格元素（inline tables）
6. 堆叠层级为 0 的子堆叠上下文（child stacking contexts）和堆叠层级为 0 的定位的后代元素
7. 堆叠层级为正的子堆叠上下文

上述关于层次的绘制规则递归地适用于任何层叠上下文。

```html
<!DOCTYPE html>
<html>
<head><style type="text/css">

div { font: 12px Arial; }

span.bold { font-weight: bold; }

div.lev1 {
   width: 250px;
   height: 70px;
   position: relative;
   border: 2px outset #669966;
   background-color: #ccffcc;
   padding-left: 5px;
}

#container1 {
   z-index: 1;
   position: absolute;
   top: 30px;
   left: 75px;
}

div.lev2 {
   opacity: 0.9;
   width: 200px;
   height: 60px;
   position: relative;
   border: 2px outset #990000;
   background-color: #ffdddd;
   padding-left: 5px;
}

#container2 {
   z-index: 1;
   position: absolute;
   top: 20px;
   left: 110px;
}

div.lev3 {
   z-index: 10;
   width: 100px;
   position: relative;
   border: 2px outset #000099;
   background-color: #ddddff;
   padding-left: 5px;
}
</style></head>

<body>

<br />

<div class="lev1">
<span class="bold">LEVEL #1</span>
   <div id="container1">
      <div class="lev2">
      <br/><span class="bold">LEVEL #2</span>
      <br/>z-index: 1;
         <div id="container2">
            <div class="lev3"><span class="bold">LEVEL #3</span></div>
            <div class="lev3"><span class="bold">LEVEL #3</span></div>
            <div class="lev3"><span class="bold">LEVEL #3</span></div>
            <div class="lev3"><span class="bold">LEVEL #3</span></div>
            <div class="lev3"><span class="bold">LEVEL #3</span></div>
            <div class="lev3"><span class="bold">LEVEL #3</span></div>
            <div class="lev3"><span class="bold">LEVEL #3</span></div>
            <div class="lev3"><span class="bold">LEVEL #3</span></div>
            <div class="lev3"><span class="bold">LEVEL #3</span></div>
            <div class="lev3"><span class="bold">LEVEL #3</span></div>
            <div class="lev3"><span class="bold">LEVEL #3</span></div>
         </div>
      </div>
      
      <div class="lev2">
      <br/><span class="bold">LEVEL #2</span>
      <br/>z-index: 1;
      </div>
   </div>
</div>

<div class="lev1">
<span class="bold">LEVEL #1</span>
</div>

<div class="lev1">
<span class="bold">LEVEL #1</span>
</div>

<div class="lev1">
<span class="bold">LEVEL #1</span>
</div>

</body></html>
```
![](https://likonion-1254082995.cos.ap-chengdu.myqcloud.com/media/16b03135bd818b83.jpg)

## 最佳实践（不犯二准则）

对于非浮层元素，避免设置 z-index 值，z-index 值没有任何道理需要超过2，原因是：

1. 定位元素一旦设置了 z-index 值，就从普通定位元素变成了层叠上下文元素，相互间的层叠顺序就发生了根本的变化，很容易出现设置了巨大的 z-index 值也无法覆盖其他元素的问题。
2. 避免 z-index “一山比一山高”的样式混乱问题。此问题多发生在多人协作以及后期维护的时候。例如，A小图标定位，习惯性写了个 z-index: 9；B一看，自己原来的实现被覆盖了，立马写了个 z-index: 99；结果比弹框组件层级还高，立马弹框组件来一个 z-index: 99999……显然，最后项目的 z-index 层级管理就是一团糟。

## 考核（荔枝FM面试题）

写出6个div元素的堆叠顺序，最上面的在第一个位置，例如: .one .two .three .four .five .six（z-index）；

```html
<div class="one">
    <div class="two"></div>
    <div class="three"></div>
</div>
<div class="four">
    <div class="five"></div>
    <div class="six"></div>
</div>
```
```css
.one {
    position: relative;
    z-index: 2;
    .two {
        z-index: 6;
    }
    .three {
        position: absolute;
        z-index: 5;
    }
}
.four {
    position: absolute;
    z-index: 1;
    .five {}
    .six {
        position: absolute;
        top: 0;
        left: 0;
        z-index: -1;
    }
}
```
答案：
```js
.three .two .one .five .six .four
```









