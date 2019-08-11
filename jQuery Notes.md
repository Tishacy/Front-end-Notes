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



## jQuery的find方法

- 查找某元素的子元素：`$(selector).find(subSelector)`

```html
<div class="father">
    <div class="son"></div>
</div>
```

```js
$('div.father').find('div.son')[0];
// <div class="son"></div>
```



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

- `$(selector).removeAttr(args)`：删除一个或多个属性节点

    ```html
    <span class="span" id="123"></span>
    ```

    ```js
    $("span").removeAttr("class id");
    // n.fn.init [span, prevObject: n.fn.init(1), context: document, selector: "span"]
    
    $("span")[0];
    // <span></span>
    ```

## jQuery-prop方法

-   `$(selector).prop(args)`：获取或设置dom元素的属性
    -   `args`为一个参数，代表获取属性的值
    -   `args`为两个参数，表示设置属性的值
    
    ```html
    <span class="line"></span>
    ```

    ```js
    $("span").prop("class");
    // "line"
    
    $('span').prop('class', 'span')
    // n.fn.init [span.span, prevObject: n.fn.init(1), context: document, selector: "span"]
    
    $('span').prop('class')
    // "span"
    ```

- `$(selector).removeAttr(args)`：删除一个或多个dom元素的属性

    ```html
    <span class="span" id="123"></span>
    ```

    ```js
    $("span").removeProp("class id");
    // n.fn.init [span, prevObject: n.fn.init(1), context: document, selector: "span"]
    
    $("span")[0];
    // <span></span>
    ```

-   注意点：

    -   prop方法不仅能操作属性，还能操作属性节点

    -   在操作属性节点时：

        -   如果具有`true`和`false`两个属性的属性节点，如`checked` `selected` `disabled`，使用`prop()`，因为输出为`true`/`false`对判断有好处
        -   反之使用`attr()`

        **例子**

        ```html
        <input type="checkbox" checked>
        <input type="checkbox">
        ```

        ```js
        console.log($("input").eq(0).prop("checked"));
        console.log($("input").eq(1).prop("checked"));
        // true
        // false
        
        console.log($("input").eq(0).attr("checked"));
        console.log($("input").eq(1).attr("checked"));
        // checked
        // undefined
        ```



## jQuery操作类（class）相关的方法

- `addClass()`：添加class，多个class之间用空格隔开
- `removeClass()`：删除class，多个class之间用空格隔开
- `toggleClass()`：切换class，即有相应的class时就删除，没有时就添加相应class，多个class之间用空格隔开

 **例子**

```html
<!-- HTML part -->
<button>box</button>
<button>red</button>
<button>blue</button>
<button>border</button>

<div></div>
```

```css
/* CSS part */
* {
    margin: 0;
    padding: 0;
}
.box {
    width: 100px;
    height: 100px;
}
.red {
    background-color: red;
}
.blue {
    background-color: blue;
}
.border {
    border: 1px solid black;
}
```

```js
// JS part
// 假设已经引入jQuery库
let btns = $("button"),
    btnNum = btns.length;
for (let i=0; i<btnNum; i++) {
    btns[i].onclick = function () {
        let className = this.innerText;
        $("div").toggleClass(className);
    }
}
```



## jQuery文本值相关操作

-   html
    -   `$(selector).html()`：获取DOM元素内部HTML信息，等价于`domElement.innerHTML`
    -   `$(selector).html(“<p>This is a paragraph.</p>”)`：给DOM元素内添加HTML，等价于`domElement.innerHTML = “<p>This is a paragraph</p>”`
-   text
    -   `$(selector).text()`：获取DOM元素内部text信息，等价于`domElement.innerText`
    -   `$(selector).text(“sentence”)`：给DOM元素内添加text，等价于`domElement.innerText = “sentence”`
-    value
    -   `$(selector).val()`：获取含value属性的DOM元素（如input元素）的value值
    -   `$(selector).val(“请输入内容”)`：给含value属性的DOM元素的value赋值



## jQuery操作css样式

-   jQuery获取css样式（可以获取dom的所有css样式）： `$(selector).css(key)`
-   jQuery设置css样式（设置的是行间样式）

    -   逐个设置：`$(selector).css(key, value)`

        ```js
        $("div").css("width", "100px");
        $("div").css("height", "100px");
        $("div").css("background-color", "red");
        ```

    -   链式设置：`$(selector).css(“width”, “100px”).css(“height”, “100px”).css(“background-color”, “red”)`

        >   注意：链式设置CSS尽量不要超过三个，否则代码阅读性太差。

    -   **批量设置（推荐）**：

        ```js
        $(selector).css({
            width: "100px",
            height: "100px",
            background-color: "red"
        });
        ```



## jQuery操作尺寸和位置

- DOM元素的宽度：
    - 获取：`$(selector).width()`
    - 设置：`$(selector).width("100px")`
- DOM元素的高度：
    - 获取：`s(selector).height()`
    - 设置：`$(selector).height(“100px”)`
- DOM元素的位置：
    - offset：元素距离窗口的偏移位置
        - 获取`$(selector).offset()`
        -   设置`$(selector).offset({top: 10, left: 10}));`
        
     ```html
    <div style="position: absolute; top: 100px; left:100px; widht: 100px; height: 100px"></div>
     ```

    ```js
    $("div").offset();
    // {top: 100, left: 100}
    
    $("div").offset({top: 200, left: 200});
    $("div").offset();
    // {top: 200, left: 200}
    ```
    
    - position：元素距离定位元素的偏移位置
    
        - 获取`$(selector).position()`
    
        >   注意：position只能获取不能设置



## jQuery的scrollTop方法

-   获取DOM元素的滚动偏移位：`$(selector).scrollTop()`
-   设置DOM元素的滚动偏移位：`$(selector).scrollTop(50)`
-   获取窗口的滚动偏移位：`$("html").scrollTop() + $("body").scrollTop()`
    -   IE和其他浏览器中，`$(“html”).scrollTop()`和`$(”body“).scrollTop()`必定有一个有值，且必定不同时有值，因此应用时将二者相加已解决兼容性问题
    
-   设置窗口的滚动偏移位：`$("html, body").scrollTop(50)`



## jQuery的事件绑定与移除方法

### 事件绑定

-   `$(selector).eventName(function () {/*...*/})` ：只支持JS部分常用事件
-   `$(selector).on(“eventName”, function () {/*...*/})`：支持JS全部的事件

注意：

-   使用jQuery对同一元素绑定的相同事件不会相互覆盖，事件触发后按绑定顺序依次执行，与原生JS中的`addEventListener`方法一致
-   原生JS中的`addEventListener`方法对同一元素绑定的相同事件之间不会相互覆盖，但是使用`on+事件名称`（比如`onclick`）方法对同一元素绑定的相同事件，后绑定的事件会覆盖之前绑定的事件。

### 事件移除

- `$(selector).off(args)`：
    - 不传参：移除该元素的所有事件
    - 传一个参数：`$(selector).off(eventName)`
        - 比如：`$(selector).off(“click”)`会移除选定元素的所有`click`事件
    - 传两个参数：`$(selector).off(eventName, eventFunc)`
        - 比如：`$(selector).off(“click”, eventFunc)`会移除选定元素所绑定的`eventFunc`的click事件

**例子**

```html
<button>Click</button>
```

```js
// Define 2 event functions.
let event1 = ()=>console.log("You hit the event1.");
let event2 = ()=>console.log("You hit the event2.");

// Add 2 click events to the button.
$("button").click(event1);
$('button').click(event2);

// Remove the click event1 from the button.
$('button').off("click", event1);
```



## jQuery事件冒泡和默认行为

-   事件冒泡：子类元素触发的事件向父类元素传播

-   阻止事件冒泡：

    -   在子类元素的事件中用`return false`
    -   `event.stopPropagation()`：阻止冒泡事件

    -   `event.returnValue = false`：兼容IE

    ```js
    $("div").click(function (event) {
        /* event function here */
        return false;
        // 或者
        // event.preventDefault();
        // event.returnValue = false;
    })
    ```
    -   默认事件：有些标签系统自带的一些事件为默认事件，比如`input:submit`的提交，`a`标签的跳转

-   阻止默认事件
    -   在所触发的事件中`return false`
    -   `event.preventDefault()`：阻止默认事件



## jQuery事件自动触发

-   `$(selector).trigger(eventName)`：自动触发事件，

    -   **会触发事件冒泡**
    -   **会触发默认事件**

    ```js
    $("div.btn").trigger("click");
    ```

-   `$(selector).triggerHandler(eventName)`：也是自动触发事件，
    -   **不会触发事件冒泡**
    -   **不会触发默认事件，`a`标签跳转除外**

    ```js
    $("div.btn").triggerHandler("click");
    ```

**面试题**：让`a`标签事件自动触发，并且跳转

```html
<a><span>自动跳转</span></a>
```

```js
// 利用span冒泡来使a标签跳转
$("span").click = function () {
    console.log("跳转");
}
$("span").trigger("click");
```



## jQuery自定义事件

-   事件必须是通过`on`绑定

-   触发事件必须通过`trigger`/`triggerHandler`触发

```html
<div></div>
```

```js
$("div").on("myClick", function () {
    console.log("Hit myClick event");
});
$("div").trigger("myClick");
```



## jQuery事件命名空间

-   事件必须通过`on绑定
-   `on`绑定的事件名称为`eventName.someone`，表示是`someone`定义的事件
    -   使用这样的命名空间不会影响原事件的触发，比如`$(selector).on(“click.name1”, eventFunc)`不会影响click事件的触发
-   触发事件必须通过`trigger`/`triggerHandler`触发

```html
<div></div>
```

```js
// 按命名空间绑定事件
$("div").on("click.pers1", ()=>console.log("pers1's click event."));
$("div").on("click.pers2", ()=>console.log("pers2's click event."));
```

```js
// 触发事件
$("div").trigger("click.pers1");
```

注意：

- 使用`trigger`触发子类的事件时，会冒泡给具有相同命名空间的事件

    **例子**

    下面的事件触发输出结果是什么

    ```html
    <div class="father">
        <div class="son"></div>
    </div>
    ```

    ```js
    // 绑定事件
    $(".father").on("click", function () {
        console.log("father no pers")
    });
    $(".father").on("click.pers1", function () {
        console.log("father pers1");
    });
    $(".father").on("click.pers2", function () {
        console.log("father pers2");
    });
    $(".son").on("click.pers1", function () {
        console.log("son pers1");
    });
    
    // 触发事件
    $(".son").trigger("click.pers1");
    ```

    输出结果：

    ```
    son pers1
    father pers1
    ```

    解析：

    -   当子类`.son`触发了`click.pers1`事件时，会冒泡给父类的`click.pers1`事件
    -   如果父类没有对应的命名空间的事件，则不会冒泡触发父类中的事件

    

## jQuery动效

### 显示/隐藏

-   显示：`$(selector).show(speed, callback)`
    -   `speed`：显示的速度
        -   数字：毫秒
        -   特定字符串：`"slow"` `"normal"` `"fast"`
    -   `callback`：回调函数，显示完之后执行的函数
    
-   隐藏：`$(selector).hide(speed, callback)` (参数同上)
-   切换：`$(selector).toggle(speed, callback)`(参数同上)

### 下拉/上收

-   下拉：`$(selector).slideDown(speed, callback) ` (参数同上)
-   上收：`$(selector).slideUp(speed, callback)` (参数同上)
-   slide切换：`$(selector).toggle(speed, callback)` (参数同上)

### 淡入/淡出

-   淡入：`$(selector).fadeIn(speed, callback)` （参数同上）
-   淡出：`$(selector).fadeOut(speed, callback)` （参数同上）
-   fade切换：`$(selector).fadeToggle(speed, callback)` (参数同上)
-   fade到：`$(selector).fadeTo(speed, opacity, callback)`：
    -   `speed`：fade的速度
    -   `opacity`：透明度 0-1
    -   `callback`：回调函数，fade结束后执行的函数

### 自定义动画

-   `$(selector).animate(args)`：

    -   接收三个参数时：为`$(selector).animate(styleObj, speed, callback)`

        -   `styleObj`：样式对象，里面可以定义需要该元素变成的css属性
        -   `speed`：动画速度，数字或特定字符串
        -   `callback`：回调函数，动画结束后执行的函数

        **例子**：给目标div设置动画：

        -   将div变成宽500px，有1px黑色实线边框的样式
        -   动画时长为1s
        -   动画结束后控制台中打印`“Animation Done.”`

        ```js
        $("div").animate({
            width: "500px",
            border: 1px solid black;
        }, 1000, ()=>console.log("Animation Done."));
        ```

    -   接收四个参数时：为`$(selector).animate(styleObj, speed, easing, callback)`

        -   在`speed`参数后面加一个参数`easing`，可以指定动画节奏：
            -   `“linear”`：匀速动画
            -   `“swing”`：先慢后快最后慢，$x-t$图像为S型曲线

### 停止/延迟

-   停止动画：`$(selector).stop(stopAll, goToEnd)`
    -   `stopAll`：（默认是`false`）是否清除动画队列
    -   `goToEnd`：（默认是`false`）是否继续完成当前动画
    >   为了防止动画队列出bug，在执行新一轮动画之前，应当先将该DOM元素的动画停止，并继续后续动画，即使用默认值直接`$(selector).stop()`

-   延迟动画：`$(selector).delay(speed)`



### 设置

-   `$.fx.off`：是否关闭页面所有jQuery动画，默认为`false`
-   `$.fx.interval`：设置jQuery动画每帧用时，默认为13（毫秒）