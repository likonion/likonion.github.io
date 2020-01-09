---
layout: post
title: JavaScript高级程序设计(第3版)读书笔记
categories: 前端技术
description: some word here
tags: JavaScript
cover: https://likonion-1254082995.cos.ap-chengdu.myqcloud.com/media/1-1F919104H63N.jpg
---
* content
{:toc}
## 第2章 在 HTML 中使用 JavaScript

### 2.1 \<script>元素

\<script>定义了下列 6 个属性：
* `async`:可选。表示应该立即下载脚本，但不应妨碍页面中的其他操作，比如下载其他资源或 等待加载其他脚本。只对外部脚本文件有效。
* `charset`:可选。表示通过 src 属性指定的代码的字符集。由于大多数浏览器会忽略它的值， 因此这个属性很少有人用。
* `defer`:可选。表示脚本可以延迟到文档完全被解析和显示之后再执行。只对外部脚本文件有 效。IE7 及更早版本对嵌入脚本也支持这个属性。
* `language`:已废弃。原来用于表示编写代码使用的脚本语言(如 JavaScript、JavaScript1.2 或 VBScript)。大多数浏览器会忽略这个属性，因此也没有必要再用了。
* `src`:可选。表示包含要执行代码的外部文件。
* `type`:可选。可以看成是 language 的替代属性;表示编写代码使用的脚本语言的内容类型(也
称为 MIME 类型)。虽然 text/javascript 和 `text/ecmascript` 都已经不被推荐使用，但人 们一直以来使用的都还是 `text/javascript`。实际上，服务器在传送 JavaScript 文件时使用的 MIME 类型通常是 application/x–javascript，但在 type 中设置这个值却可能导致脚本被 忽略。另外，在非 IE 浏览器中还可以使用以下值:application/javascript 和 `application/ecmascript`。考虑到约定俗成和最大限度的浏览器兼容性，目前 type 属性的值依旧还是 text/javascript。不过，这个属性并不是必需的，如果没有指定这个属性，则其默认值仍为 text/javascript。

### 2.5 小结

* 在包含外部 JavaScript 文件时，必须将 src 属性设置为指向相应文件的 URL。而这个文件既可 以是与包含它的页面位于同一个服务器上的文件，也可以是其他任何域中的文件。
* 所有`<script>`元素都会按照它们在页面中出现的先后顺序依次被解析。在不使用 defer 和 async 属性的情况下，只有在解析完前面`<script>`元素中的代码之后，才会开始解析后面 `<script>`元素中的代码。
* 由于浏览器会先解析完不使用 defer 属性的`<script>`元素中的代码，然后再解析后面的内容， 所以一般应该把`<script>`元素放在页面最后，即主要内容后面，`</body>`标签前面。
* 使用 defer 属性可以让脚本在文档完全呈现之后再执行。延迟脚本总是按照指定它们的顺序执行。
* 使用 async 属性可以表示当前脚本不必等待其他脚本，也不必阻塞文档呈现。不能保证异步脚
本按照它们在页面中出现的顺序执行。

## 第3章 基本概念

### 3.1.2 标识符
所谓标识符，就是指变量、函数、属性的名字，或者函数的参数。标识符可以是按照下列格式规则 组合起来的一或多个字符:
* 第一个字符必须是一个**字母**、下划线(**_**)或一个美元符号(**$**);
* 其他字符可以是字母、下划线、美元符号或数字

### 3.1.4 严格模式
要在整个脚本中启用严格模式，可以在顶部添加如下代码:
```js
    "use strict";
```
在函数内部的上方包含这条编译指示，也可以指定函数在严格模式下执行:
```js
    function doSomething(){
        "use strict";
        //函数体
    }
```

### 3.8 小结

* ECMAScript 中的基本数据类型包括 `Undefined`、`Null`、`Boolean`、`Number` 和 `String`。
* 与其他语言不同，ECMScript 没有为整数和浮点数值分别定义不同的数据类型，`Number` 类型可
用于表示所有数值。
* ECMAScript 中也有一种复杂的数据类型，即 `Object` 类型，该类型是这门语言中所有对象的基
础类型。
* 严格模式为这门语言中容易出错的地方施加了限制。
* ECMAScript 提供了很多与 C 及其他类 C 语言中相同的基本操作符，包括算术操作符、布尔操作
   符、关系操作符、相等操作符及赋值操作符等。
* ECMAScript 从其他语言中借鉴了很多流控制语句，例如 `if` 语句、`for` 语句和 `switch` 语句等。 ECMAScript 中的函数与其他语言中的函数有诸多不同之处。
* 无须指定函数的返回值，因为任何 ECMAScript 函数都可以在任何时候返回任何值。
* 实际上，未指定返回值的函数返回的是一个特殊的 `undefined` 值。
* ECMAScript 中也没有函数签名的概念，因为其函数参数是以一个包含零或多个值的数组的形式传递的。
* 可以向 ECMAScript 函数传递任意数量的参数，并且可以通过 `arguments` 对象来访问这些参数。
* 由于不存在函数签名的特性，ECMAScript 函数不能重载。

## 第4章 变量、作用域和内存问题
### 4.4 小结
* 基本类型值在内存中占据固定大小的空间，因此被保存在栈内存中;
* 从一个变量向另一个变量复制基本类型的值，会创建这个值的一个副本;
* 引用类型的值是对象，保存在堆内存中;
* 包含引用类型值的变量实际上包含的并不是对象本身，而是一个指向该对象的指针;
* 从一个变量向另一个变量复制引用类型的值，复制的其实是指针，因此两个变量最终都指向同
一个对象;
* 确定一个值是哪种基本类型可以使用 typeof 操作符，而确定一个值是哪种引用类型可以使用 instanceof 操作符。
所有变量(包括基本类型和引用类型)都存在于一个执行环境(也称为作用域)当中，这个执 行环境决定了变量的生命周期，以及哪一部分代码可以访问其中的变量。以下是关于执行环境的几 点总结:
* 执行环境有全局执行环境(也称为全局环境)和函数执行环境之分;
* 每次进入一个新执行环境，都会创建一个用于搜索变量和函数的作用域链;
* 函数的局部环境不仅有权访问函数作用域中的变量，而且有权访问其包含(父)环境，乃至全
局环境;
* 全局环境只能访问在全局环境中定义的变量和函数，而不能直接访问局部环境中的任何数据;
* 变量的执行环境有助于确定应该何时释放内存。
JavaScript 是一门具有自动垃圾收集机制的编程语言，开发人员不必关心内存分配和回收问题。可 以对 JavaScript 的垃圾收集例程作如下总结。
* 离开作用域的值将被自动标记为可以回收，因此将在垃圾收集期间被删除。
* “标记清除”是目前主流的垃圾收集算法，这种算法的思想是给当前不使用的值加上标记，然
后再回收其内存。
* 另一种垃圾收集算法是“引用计数”，这种算法的思想是跟踪记录所有值被引用的次数。JavaScript
引擎目前都不再使用这种算法;但在 IE 中访问非原生 JavaScript 对象(如 DOM 元素)时，这种
   算法仍然可能会导致问题。
* 当代码中存在循环引用现象时，“引用计数”算法就会导致问题。
* 解除变量的引用不仅有助于消除循环引用现象，而且对垃圾收集也有好处。为了确保有效地回
   收内存，应该及时解除不再使用的全局对象、全局对象属性以及循环引用变量的引用。

## 第5章 引用类型
### 5.2 Array类型
#### 5.2.5 重排序方法
* `reverse()` :反转数组项的顺序。
* `sort()` :方法按升序排列数组项——即最小的值位于最前面，最大的值排在最后面。
    ```js
    var values = [0, 1, 1, 5, 8, 9, 5, 10, 15];
    values.sort((value1,value2)=>{return value2-value1})
    ```

#### 5.2.6 操作方法
* `concat()` :创建当前数组一个副本，然后将接收到的参数 添加到这个副本的末尾，最后返回新构建的数组。
```js
 var colors = ["red", "green", "blue"];
    var colors2 = colors.concat("yellow", ["black", "brown"]);
    alert(colors);     //red,green,blue
    alert(colors2);    //red,green,blue,yellow,black,brown
```
* `slice()` :在只有一个参数的情况下，slice()方法返回从该 参数指定位置开始到当前数组末尾的所有项。如果有两个参数，该方法返回起始和结束位置之间的项— —但不包括结束位置的项。
```js
var colors = ["red", "green", "blue", "yellow", "purple"];
var colors2 = colors.slice(1);
var colors3 = colors.slice(1,4);
alert(colors2);   //green,blue,yellow,purple
alert(colors3);   //green,blue,yellow
```
* `splice()` :
    * **删除**:可以删除任意数量的项，只需指定 2 个参数:要删除的第一项的位置和要删除的项数。 例如，`splice(0,2)`会删除数组中的前两项。
    * **插入**:可以向指定位置插入任意数量的项，只需提供 3 个参数:起始位置、0(要删除的项数) 和要插入的项。如果要插入多个项，可以再传入第四、第五，以至任意多个项。例如， `splice(2,0,"red","green")`会从当前数组的位置 2 开始插入字符串"red"和"green"。
    * **替换**:可以向指定位置插入任意数量的项，且同时删除任意数量的项，只需指定 3 个参数:起 始位置、要删除的项数和要插入的任意数量的项。插入的项数不必与删除的项数相等。例如， `splice (2,1,"red","green")`会删除当前数组位置 2 的项，然后再从位置 2 开始插入字符串 "red"和"green"。
    ```js
    var colors = ["red", "green", "blue"];
    var removed = colors.splice(0,1); alert(colors); // green,blue alert(removed); // red，返回的数组中只包含一项
    removed = colors.splice(1, 0, "yellow", "orange"); alert(colors); // green,yellow,orange,blue alert(removed); // 返回的是一个空数组
    removed = colors.splice(1, 1, "red", "purple"); alert(colors); // green,red,purple,orange,blue alert(removed); // yellow，返回的数组中只包含一项
    // 删除第一项
    // 从位置 1 开始插入两项
    // 插入两项，删除一项
    ```

#### 5.2.7 位置方法
ECMAScript 5 为数组实例添加了两个位置方法:`indexOf()`和 `lastIndexOf()`。这两个方法都接收 两个参数:要查找的项和(可选的)表示查找起点位置的索引。其中，`indexOf()` 方法从数组的开头(位 置 0)开始向后查找，`lastIndexOf()` 方法则从数组的末尾开始向前查找。
> 使用 indexOf()和 lastIndexOf()方法查找特定项在数组中的位置非常简单，支持它们的浏览器包 括 IE9+、Firefox 2+、Safari 3+、Opera 9.5+和 Chrome。

#### 5.2.8 迭代方法
* `every()` :对数组中的每一项运行给定函数，如果该函数对每一项都返回 true，则返回 true。
* `filter()` :对数组中的每一项运行给定函数，返回该函数会返回 true 的项组成的数组。
* `forEach()` :对数组中的每一项运行给定函数。这个方法没有返回值。
* `map()` :对数组中的每一项运行给定函数，返回每次函数调用的结果组成的数组。
* `some()` :对数组中的每一项运行给定函数，如果该函数对任一项返回 true，则返回 true。
> 支持这些迭代方法的浏览器有 IE9+、Firefox 2+、Safari 3+、Opera 9.5+和 Chrome。

### 5.5 Function类型
#### 5.5.4 函数内部属性
在函数内部，有两个特殊的对象:arguments 和 this。其中，arguments 在第 3 章曾经介绍过， 12 它是一个类数组对象，包含着传入函数中的所有参数。虽然 arguments 的主要用途是保存函数参数， 但这个对象还有一个名叫 callee 的属性，该属性是一个指针，指向拥有这个 arguments 对象的函数。
```js
 function factorial(num){
        if (num <=1) {
            return 1;
        } else {
            return num * factorial(num-1)
        }
}
function factorial(num){
    if (num <=1) {
        return 1;
    } else {
        return num * arguments.callee(num-1)
    }
}
```

## 第13章 事件
### 13.3 事件对象
#### 13.3.1 DOM中的事件对象
event 对象包含与创建它的特定事件有关的属性和方法。触发的事件类型不一样，可用的属性和方
 法也不一样。不过，所有事件都会有下表列出的成员。

|属性/方法| 类 型 | 读/写 | 说明 |
|---|---|---|---|
|bubbles|Boolean|只读|表明事件是否冒泡|
|cancelable|Boolean|只读|表明是否可以取消事件的默认行为|
|currentTarget|Element|只读|其事件处理程序当前正在处理事件的那个元素|
|defaultPrevented|Boolean|只读|为 true 表 示 已 经 调 用 了 preventDefault() (DOM3级事件中新增)|
|detail|Integer|只读|与事件相关的细节信息|
|eventPhase|Integer|只读|调用事件处理程序的阶段:1表示捕获阶段，2表示“处于目标”，3表示冒泡阶段|
|preventDefault()|Function|只读|取消事件的默认行为。如果cancelable是true，则可以使用这个方法|
|stopImmediatePropagation()|Function|只读|取消事件的进一步捕获或冒泡，同时阻止任何事件处理程序被调用(DOM3级事件中新增)|
|stopPropagation()|Function|只读|取消事件的进一步捕获或冒泡。如果bubbles为true，则可以使用这个方法|
|target|Element|只读|事件的目标|
|trusted|Boolean|只读|为true表示事件是浏览器生成的。为false表 示事件是由开发人员通过JavaScript创建的 (DOM3级事件中新增)|
|type|String|只读|被触发的事件的类型|
|view|AbstractView|只读|与事件关联的抽象视图。等同于发生事件的 window对象|