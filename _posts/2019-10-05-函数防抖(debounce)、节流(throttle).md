---
layout: post
title: 函数防抖(debounce)、节流(throttle)
categories: 前端技术
description: some word here
keywords: debounce, throttle
tags: debounce throttle
cover: https://likonion-1254082995.cos.ap-chengdu.myqcloud.com/media/thumb.png
---

函数防抖和函数节流：优化高频率执行js代码的一种手段，js中的一些事件如浏览器的resize、scroll，鼠标的mousemove、mouseover，input输入框的keypress等事件在触发时，会不断地调用绑定在事件上的回调函数，极大地浪费资源，降低前端性能。为了优化体验，需要对这类事件进行调用次数的限制。

## 函数防抖

>  **在事件被触发n秒后再执行回调，如果在这n秒内又被触发，则重新计时。**

1. 实现方式

    利用闭包，设置一个timer：

    * 每次执行都 `clearTimeout(timer)`
    * 每次执行设置 `timer = setTimeout()` 执行函数
    ```js
    function debounce(func, delay) {
        // 存储timer
        let timer = null
        return function() {
            // 如果已经设置了 timer，将其清空，防止未执行的timer执行
            clearTimeout(timer) 
            timer = setTimeout(()=> {

                // delay后执行函数，使用es6语法，给函数传入参数
                let args = Array.from(arguments)

                // 传入this和参数
                func.apply(this, args)

            }, delay)
        }
    }               
    ```
2. 实例
    
    在input事件使用 `debounce` ，减少触发次数，这里设置延迟为1秒，是不是有点像搜索引擎的提示呈现
    ![](https://likonion-1254082995.cos.ap-chengdu.myqcloud.com/media/GIF.gif)
    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>Document</title>
    </head>
    <body>
    <input type="text" placeholder="debounce"/>
    <p>debounce test</p>
    </body>
    </html>

    <script>
    const input = document.querySelector('input')
    const p = document.querySelector('p')
    input.addEventListener('input', debounce(function(e){
    p.innerText = e.target.value
    }, 1000))


    function debounce(func, delay) {
    // 存储timer
    let timer = null
    return function() {
        // 如果已经设置了 timer，将其清空，防止未执行的timer执行
        clearTimeout(timer) 
        timer = setTimeout(()=> {

            // delay后执行函数，使用es6语法，给函数传入参数
            let args = Array.from(arguments)

            // 传入this和参数
            func.apply(this, args)

        }, delay)
    }
    }

    </script>
    ```

## 函数节流

>  **每隔一段时间，只执行一次函数。**

1. 实现方式

    利用闭包，设置一个exec记录当前函数执行状态：

    * 若正则执行，则跳过if，直接退出函数
    * 若没有在执行，则执行函数，设置exec为true，一段时间后置为false
    ```js
    function throttle(func, delay) {
        // 记录当前函数是否正在被执行
        let exec = false
        return function() {
            // 如果该函数未在执行
            if (!exec){
            // 执行该函数
            let args = Array.from(arguments)
            func.apply(this, args)
            
            // 设置执行为true
            exec = true

            // 在delayh后设置为false
            setTimeout(()=>{
                exec = false
            }, delay)
            }
        }
    }
    ```
2. 实例

    这里同样使用上面的例子，看看有什么区别
    * 使用throttle，设置1秒只能触发一次
    ![](https://likonion-1254082995.cos.ap-chengdu.myqcloud.com/media/GIF-1564489890628.gif)


    如果还看不出区别，请看下图
    * 使用debounce ，设置延迟为1秒
    ![](https://likonion-1254082995.cos.ap-chengdu.myqcloud.com/media/GIF-1564489668561.gif)

    很明显了对吧

    不过throttle一般不用于这里，一般用于scroll，mousemove等触发频率很高的事件，对其进行限制

## 异同比较

相同点：

* 都可以通过使用 `setTimeout` 实现。
* 目的都是，降低回调执行频率。节省计算资源。

不同点：

* 函数防抖，在一段连续操作结束后，处理回调，利用 `clearTimeout` 和 `setTimeout` 实现。函数节流，在一段连续操作中，每一段时间只执行一次，频率较高的事件中使用来提高性能。
* 函数防抖关注一定时间连续触发的事件只在最后执行一次，而函数节流侧重于一段时间内只执行一次。

## 常见应用场景

1. **函数防抖的应用场景**

    连续的事件，只需触发一次回调的场景有：

    * 搜索框搜索输入。只需用户最后一次输入完，再发送请求
    * 手机号、邮箱验证输入检测
    * 窗口大小 `Resize` 。只需窗口调整完成后，计算窗口大小。防止重复渲染。

2. **函数节流的应用场景**

    间隔一段时间执行一次回调的场景有：

    * 滚动加载，加载更多或滚到底部监听
    * 谷歌搜索框，搜索联想功能
    * 高频点击提交，表单重复提交
