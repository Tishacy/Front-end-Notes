<h1 style="fontsize:28px;text-align:center;font-family:Georgia;font-style:italic;color:#333">jQuery Notes</h1>

[TOC]

# jQuery简介

-   jQuery是一个JavaScript库
-   使用jQuery能够使HTML文档遍历和操作、事件处理、动画以及ajax更加方便
-   jQuery版本：
    -   1.x：（优先选择）兼容IE678，相对其他版本文件较大，官方只做bug维护，功能不再新增
    -   2.x：不兼容IE678，相对1.x文件较小，官方只做bug维护，功能不再新增
    -   3.x：不兼容IE678，只支持最新的浏览器，很多老的jQuery插件不支持这个版本，相对1.x文件较小，提供不包含ajax/动画api版本。



# jQuery的使用

## 下载使用

1.  下载jQuery：[jQuery下载地址](http://code.jquery.com/)

2.  引入下载的jQuery库

    ```html
    <script src="js/jquery-1.12.4.js"></script>
    ```

3.  编写jQuery代码

    ```html
    <script>
    	$(document).ready(function () {});
    </script>
    ```

## 非下载使用

1.  引入在线jQuery库

    ```html
    <script src="http://code.jquery.com/jquery-1.12.4.min.js"></script>
    ```

2.  编写jQuery代码



# jQuery基础

## jQuery的冲突问题

在引入jQuery库之后，如果还引入其他也使用`$`的库，那么就会造成冲突问题。可以通过以下方法来解决冲突问题

1. 释放`$`的使用权
    ```js
    jQuery.noConflict();
    ```

    -   释放操作必须在编写其他jQuery代码之前
    -   释放之后就不能再使用`$`，而改为使用`jQuery`

2.  自定义一个访问符号

    ```js
    var sig = jQuery.noConflict();
    sig(function() {
        alert("hello sig");
    })
    ```



## jQuery的核心函数

jQuery的核心函数是：

```js
$(args);
```

其中参数`args`：

-   接收一个函数`callback`：相当于`$(document).ready(callback)`

    即：等待dom树构建完成后就执行的代码，即入口函数

    ```js
    $(function () {
        alert("hello jquery.");
    });
    
    // 等价于
    $(document).ready(function() {
        alert("hello jquery.");
    })
    ```

-   接收一个字符串

    -   接收一个字符串选择器（CSS选择器，可以组合），得到DOM元素列表（jQuery对象，类数组）

        ```js
        $('img');
        $('.className');
        $('#idName');
        $('parent child');
        ```

    -   接收一个html片段，会创建相应的DOM元素

        ```js
        $("<p>This is a paragraph</p>");
        ```

-   接收一个DOM元素，会被包含成为一个jQuery对象（类数组）

    ```js
    var box = document.getElementsByClassName('demo')[0];
    var $box = $(box);
    console.log($box);
    // n.fn.init [div#box.demo, context: div#box.demo]
    ```



## jQuery的静态方法

### jQuery的each方法

-   原生JS中的**数组**的forEach方法，没有返回值

    ```js
    let arr = [1, 2, 3, 4, 5];
    arr.forEach(function (value, index) {
        console.log(index, value);
    })
    // 0 1
    // 1 2
    // 2 3
    // 3 4
    // 4 5
    ```

- 原生JS中**类数组**的forEach方法：通过`Array.prototype.forEach`来实现，没有返回值

    ```js
    function List() {
        this.length = 0;
        this.push = Array.prototype.push;
        this.splice = Array.prototype.splice;
        this.forEach = Array.prototype.forEach;	// *
    }
    
    let list = new List();
    for (let i=0; i<5; i++) {
        list.push(i+1);
    }
    list.forEach((value, index)=>console.log(index, value));
    // 0 1
    // 1 2
    // 2 3
    // 3 4
    // 4 5
    ```

-   jQuery的each静态方法：

    `$.each(arr, callback)$`

    -   `arr`：需要遍历的数组，可以是数组或者类数组
    -   `callback(index, value)`：回调函数，注意与原生的`Array.prototype.forEach`的回调函数的参数顺序不一样
    -   返回值：返回传入的`arr`

```js
let arr = [1, 2, 3, 4, 5];
$.each(arr, (index, value)=>console.log(index, value));
// 0 1
// 1 2
// 2 3
// 3 4
// 4 5

let list = {0:1, 1:2, 2:3, 3:4, 4:5, length:5};
$.each(list, (index, value)=>{console.log(index, value)});
// 0 1
// 1 2
// 2 3
// 3 4
// 4 5
```



### jQuery的map方法

-   原生JS的map方法：只能遍历数组，类数组不可以

    ```js
    let arr = [1, 2, 3, 4, 5];
    let newArr = arr.map(function (value, index, array) {
        console.log(index, value, array);
        return value+10;
    });
    console.log(newArr);
    // [11, 12, 13, 14, 15]
    ```

-   jQuery中的map方法：数组/类数组都可以遍历

    -   数组

        ```js
        let arr = [1, 2, 3, 4, 5];
        let newArr = $.map(arr, function (value, index) {
             return value+10;
         });
        console.log(newArr);
        // [11, 12, 13, 14, 15]
        ```
    
    -   类数组：返回一个真数组
    
        ```js
        let obj = {0:1, 1:2, 2:3, 3:4, 4:5, length:5};
        let newObj = $.map(obj, function (value, index) {
            return value+10;
        });
        console.log(newObj);
        // [11, 12, 13, 14, 15]
        ```
    
        

###  jQuery中的其他静态方法

-   `$.trim(string)`：去除字符串两边的空格，返回处理后的字符串

-   `$.isWindow(obj)：判断是否为`window`对象

-   `$.isArray(obj)`：判断是否为真数组（不包括类数组）

-   `$.isFunction(obj)`：判断是否为函数（包括构造函数、立即执行函数等）

-   `$.holdReady(boolean)`：

    -   传入`true`：暂停ready执行代码
    -   传入`false`：恢复ready执行代码

    >   ready执行
    >
    >   `$(document).ready(function () {/* ready执行的代码 */});`



## jQuery的内容选择器

假设有html结构如下：

```html
<div id="1"></div>
<div id="2">  </div>
<div id="3"><!--This is a comment--></div>
<div id="4">This is text.</div>
<div id="5">
    <span></span>
</div>
```

- `:parent`: 找到有文本内容或者有子元素的指定元素

    ```js
    let d = $("div:parent");
    
    console.log(d);
    // n.fn.init [div#2, prevObject: n.fn.init(1), context: document, selector: "div:parent"]
    
    console.log(d[0]);
    // <div id="2">  </div>
    ```

-   `:empty`：找到既没有文本元素也没有子元素的指定元素（可以包含注释节点）

    ```js
    let d = $("div:empty");
    
    ```

console.log(d);
    // n.fn.init(2) [div#1, div#3, prevObject: n.fn.init(1), context: document, selector: "div:empty"]
    ```
    
- `:contains(text)`：找到包含某段文本的指定元素

    ```js
    let d = $("div:contains('This')");
    
    console.log(d);
    // n.fn.init [div#4, prevObject: n.fn.init(1), context: document, selector: "div:contains("This")"]
    ```

-   `:has(selector)`：找到包含指定子元素的指定元素

    ```js
    let d = $("div:has('span')");
    
    console.log(d);
    // n.fn.init [div#5, prevObject: n.fn.init(1), context: document, selector: "div:has("span")"]
    ```

 

## jQuery-attr方法

-   `$(selector).attr(args)`：获取或者设置属性节点的值

    -   `args`为一个参数，代表**获取**属性节点的值
    -   `args`为两个参数，代表**设置**属性节点的值

    ```html
    <span class="line"></span>
    ```

    ```js
    $("span").attr("class");
    // "line"
    
    $('span').attr('class', 'span')
    // n.fn.init [span.span, prevObject: n.fn.init(1), context: document, selector: "span"]
    
    $('span').attr('class')
    // "span"
    ```

    

- 

