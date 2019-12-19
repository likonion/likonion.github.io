---
layout: post
title: 移动端开发遇到的坑
categories: 前端技术
description: some word here
keywords: mobile, 移动端
cover: https://likonion-1254082995.cos.ap-chengdu.myqcloud.com/media/a7aa6ff5435d5e1_size59_w1200_h799.jpg
---

### body、document、window的click事件无效处理办法
在做移动端效果的时候，会碰到做弹窗的效果，弹窗之后点击空白处关闭弹窗，但是苹果手机上的QQ浏览器UC浏览器都点击body、document、window无效。最后找到了解决办法，

就是给要点击的元素加个css样式

`cursor：pointer；`

 当然，document和window是不能给的。 所以只能给body加个此属性即可。暂时还未发现别的解决办法。（虽然这样解决了问题，但是

又发现另一个问题，就是点击页面任意地方，都会闪一下蓝色，解决办法：给body加

```
-webkit-tap-highlight-color: rgba(0,0,0,0); 
-webkit-tap-highlight-color:transparent;）
```


