<h1 style="fontsize:28px;text-align:center;font-family:Georgia;fonat-style:italic;color:#333">DOM Learning Notes</h1>
[TOC]

# 什么是DOM

- DOM （Document Object Model）：文档对象模型
- DOM定义了表示和修改文档所需的方法。DOM对象即为宿主对象，由浏览器厂商定义，用来**操作html和xml功能的一类对象的集合**。
- 也有人称DOM是对html和xml的**标准编程接口**。



# DOM继承树

<img src="./Notes images/dom继承树.png">

**实例：**

```js
console.log(document.__proto__); // HTMLDocument.prototype
console.log(document.__proto__.__proto__); // Document.prototype
console.log(document.__proto__.__proto__.__proto__); // Node.prototype
console.log(document.__proto__.__proto__.__proto__.__proto__); // EventTarget.prototype
console.log(document.__proto__.__proto__.__proto__.__proto__.__proto__); // Object.prototype
```



# DOM基本操作

## 对节点的增删改查

### 节点

#### 节点的四个属性

- `nodeName`：只读
  - 元素的标签名，以大写字符串形式表示
  - 其他节点的`nodeName`是`#text #comment`等。
- `nodeValue`：text节点和comment节点才有这个
- `nodeType`：该节点的类型，只读
  - 元素节点 —— 1
  - 属性节点 —— 2
  - 文本节点 —— 3
  - 注释节点 —— 8
  - document —— 9
    - document代表整个文档
    - `html`标签只是document的根标签，而不代表整个文档	
  - DocumentFragment ——11
- `attributes`： 该节点的属性节点集合（类数组）。
  - 可以改属性值，不能改属性名

#### 节点的一个方法

`Node.hasChildNodes();`: 某节点是否含有子节点。



#### 元素节点的一些属性

- innerHTML
- innerText （老版本火狐不兼容）/ textContent （老版本IE不好使）

#### 元素节点的一些方法

- `element.setAttribute(key, value);`
- `element.getAttribute(key, value);`

```js
// 设 div 是标签 <div></div>
div.setAttribute('class', 'demo');
console.log(div);
// <div class="demo"></div>

var divClass = div.getAttribute('class');
console.log(divClass);
// "demo"
```





### 查找

#### 使用方法（method）查找

- `document.getElementById('id名')`：选出id为`'id名'`的元素。

  - 由于html中的所有标签的id名不可以重复，一个id只能代表一个元素，因此这个方法得到的只是一个元素，而不是元素列表。
  - 在IE8以下的浏览器，不区分id大小写， 而且也返回匹配name属性的元素。

- `document.getElementsByTagName('tag名')`：根据标签名选择元素列表

  - 由于标签名是可以重复的，因此返回选中的元素列表。
  - 返回的元素列表不是数据，而是一个类数组。
  - 兼容性最好，**最主流的用法**

- `document.getElementsByClassName('class名')`：根据class属性选择元素列表。

  - IE8和IE8以下的IE版本中没有，因此没有`getElementsByTagName`常用。

- `document.getElementsByName('name')`：根据name属性选择元素，返回元素列表。

  - name属性是为了提交数据用的，比如表单、img、frame：

      ```html
      <input name="userid">
      ```

      ```js
      var input = document.getElementsByName('userid')[0];
      console.log(input);
      // <input name="userid">
      ```

- `document.querySelector('css selector')`：根据css选择器来选择元素，返回一个元素。比如：

  ```html
  <div>
  	<p>111</p>
  </div>
  <div>
  	<p>222</p>
  </div>
  ```

  ```js
  var p = document.querySelector('div p');
  console.log(p);
  // <p>111</p>
  ```

  - 在IE7和IE7以下的版本中没有
  - 虽然很强大，但是实际开发中不怎么用，是因为它有实时性问题，并不同步更新。
    - 就是说如果在使用该方法获取了元素a之后，页面中的元素a发生了变化，那么获取到的元素a并不同步变化，相当于只是做了个未更改前的元素a的拷贝（类似副本、快照）。
    - 而其他的元素查找方法则返回的是元素的引用，是可以同步变化的，因此别的查找方法更常用。

- `document.querySelectorAll('css selector')`：根据css选择器来选择元素，返回一个元素列表。比如：

  ```html
  <div>
  	<p>111</p>
  </div>
  <div>
  	<p>222</p>
  </div>
  ```

  ```js
  var ps = document.querySelectorAll('div p');
  for (var i=0; i<ps.length; i++){
      console.log(ps[i]);
  }
  // <p>111</p>
  // <p>222</p>
  ```

  - 在IE7和IE7以下的版本中没有
  - 实际开发中不常用，原因同`querySelector()`。



#### 使用非方法查找

- 遍历**节点树**

  > 任何一个浏览器都好使

  - `parentNode` 父节点（最顶端的父节点是#document）
  - `childNodes` 子节点们
    - `firstChild` 第一个子节点
    - `lastChild` 最后一个子节点
  - `nextSibling` 后一个兄弟节点
  - `previousSibling` 前一个兄弟节点

  **注意**

  1. 节点树包含所有类型的节点。包括以下节点类型：
     - 元素节点 —— 1，如 `<div></div>`
     - 属性节点 —— 2  如 `id="123"`
     - 文本节点 —— 3  `plain text\n` 
     - 注释节点 —— 8 `<!-- This is comment -->`
     - document —— 9
     - DocumentFragment ——11

  **实例**

  ```html
  <div>
    <span>
      <strong></strong>
    </span>
    <p></p>
  </div>
  
  <script>
    // parentNode 
    console.log('parent:')
  	var strong = document.getElementsByTagName('strong')[0];
    console.log(strong.parentNode);
    console.log(strong.parentNode.parentNode);
    console.log(strong.parentNode.parentNode.parentNode);
    console.log(strong.parentNode.parentNode.parentNode.parentNode);
    console.log(strong.parentNode.parentNode.parentNode.parentNode.parentNode);
    console.log(strong.parentNode.parentNode.parentNode.parentNode.parentNode.parentNode);
    
    // childNodes, firstChild, lastChild
    console.log('\nchild:')
    var div = document.getElementsByTagName('div')[0];
    var divChilds = div.childNodes;
    for (var i=0; i<divChilds.length; i++) {
      console.log(divChilds[i]);
    }
    console.log("fist child is " + div.firstChild);
    console.log("last child is " + div.lastChild);
    
    // nextSibling, previousSibling
    console.log('\nsibling:');
    var span = document.getElementsByTagName('span')[0];
    console.log(span.nextSibling);
    console.log(span.previousSibling);
  </script>
  ```

  ```html
  parent:
  <span></span>
  <div></div>
  <body></body>
  <html></html>
  #document
  null
  
  child:
  #text
  <span></span>
  #text
  <p></p>
  #text
  first child is [object Text]
  last child is [object Text]
  
  sibling:
  #text
  #text
  ```

  

- 基于元素节点树的遍历

  > 除了`children`以外，在IE9及IE9以下版本不兼容。

  - `parentElement` 返回当前元素的父元素节点
    - 注意：#document不是元素节点，是单独的一个节点，因此`html`的`parentElement`是`null`
  - `children` 会返回当前元素的元素子节点
    - `childElementCount` 完全等于`children.length` 当前元素节点的子元素节点数
    - `firstElementChild` 第一个子元素节点
    - `lastElementChild` 最后一个子元素节点
  - `nextElementSibling` 后一个兄弟元素节点
  - `previousElementSibling` 上一个兄弟元素节点

  **注意**

  1. 元素节点树中只含有元素节点。

  **实例**

  ```html
  <div>
    <span>
      <strong></strong>
    </span>
    <p></p>
  </div>
  
  <script>
  	// parentElement
    console.log('parent:')
    var strong = document.getElementsByTagName('strong')[0];
    console.log(strong.parentElement);
    console.log(strong.parentElement.parentElement);
    console.log(strong.parentElement.parentElement.parentElement);
    console.log(strong.parentElement.parentElement.parentElement.parentElement);
    console.log(strong.parentElement.parentElement.parentElement.parentElement.parentElement);
    
    // child
    console.log('\nchild:');
    var div = document.getElementsByTagName('div')[0];
  	var children = div.children;
    for (var i=0; i<children.length; i++) {
      console.log(children[i]);
    }
    console.log('first element child is ' + div.firstElementChild);
    console.log('last element child is ' + div.lastElementChild);
    
    // sibling
    console.log('\nsibling:');
    var span = document.getElementsByTagName('span')[0];
    console.log(span.nextElementSibling);
    console.log(span.previousElementSibling);
  </script>
  ```

  ```html
  parent:
  <span></span>
  <div></div>
  <body></body>
  <html></html>
  null
  
  child:
  <span></span>
  <p></p>
  first element child is [HTMLSpanElement]
  last element child is [HTMLParagraphElement]
  
  sibling:
  <p></p>
  null
  ```



#### 注意

<img src="./Notes images/dom继承树.png">

1. `getElementById`方法定义在`Document.prototype`上。即：

   - `Document`节点可以使用该方法，而`Element`节点没有该方法。

     ```js
     document.getElementById('123'); // √
     // var div = document.getElementsByTagName('div')[0];
     div.getElementById('123'); // ×
     ```

2. `getElementByName`方法定义在了`HTMLDocument.protype上`。即：

   - 非html中的document不能使用该方法（XMLDocument, Element）

3. `getElementByTagName`方法定义在了`Document.prototype`和`Element.prototype`上。

   - `Document`节点和`Element`节点都可以使用该方法。

     ```js
     var div = document.getElementsByTagName('div')[0];
     var span = div.getElementsByTagName('span')[0];
     ```

4. ` HTMLDocument.prototype`定义了一些常用的属性，`body` `head`分别指代HTML文档中的`<body>` `<head>`标签。

5. `Document.prototype`上定义了`documentElement`属性，只带文档的根元素，在HTML文档中，它总是指代`<html>`元素。

6. `getElementsByClassName` `querySelector` `querySelectorAll` 在`Document.prototype`和`Element.prototype`上均有定义。



#### 查找节点的练习

1. 遍历元素节点树，在原型链上编程

   ```js
   // 遍历节点树
   Element.prototype.getElementsTree = function () {
       var children = this.children,
           len = children.length;
       for (var i=0; i<len; i++) {
           console.log(children[i]);
           if (children[i].hasChildNodes()) {
               children[i].getElementsTree();
           }   
       }
   }
   ```
   
2. 封装函数，返回元素e的第n层祖先元素节点

   ```js
   // 使用递归
   function getParent(e, n) {
       return (e == null)? null : ((n>0)? getParent(e.parentElement, n-1) : e);
   };
   
   // 递归的另一种写法
   function getParent(e, n) {
     if (e == null || n == 0) {
       return e;
     }else if (n > 0) {
       return getParent(e.parentElement, n-1);
   }
     
   // 使用循环
   function getParent(e, n) {
   	while (n && e) {
       e = e.parentElement;
       n--;
     }
     return e;
   }
   ```

3. 封装函数，返回元素e的第n个兄弟元素节点，n为正，返回后面的兄弟元素节点，n为负，返回前面的，n为0，返回自身。

   ```js
   // 使用递归
   function getSibling(e, n) {
   	if (n == 0 || e == null) {
   		return e;
   	}else	if (n < 0) {
   		return getSibling(e.previousElementSibling, n+1);
   	}else if (n > 0) {
   		return getSibling(e.nextElementSibling, n-1);
   }
     
   // 使用迭代
   function getSibling(e, n) {
       while (n && e) {
           if (n > 0) {
               e = e.nextElementSibling;
               n -- ;
           }else {
   						e = e.previousElementSibling;
               n ++;
           }
       }
       return e;
   }
   ```

4. 编辑函数，封装`myChildren`功能，解决以前部分浏览器的兼容性问题

   ```js
   Element.prototype.getChildren = function (){
       var childNodes = this.childNodes,
           len = childNodes.length;
       var children = {
           length: 0,
           push: Array.prototype.push,
           splice: Array.prototype.splice
       }
       for (var i=0; i<len; i++) {
           if (childNodes[i].nodeType == 1) {
               children.push(childNodes[i]);
        }
       }
       return children
   }
   
   var div = document.getElementsByTagName('div')[0];
   console.log(div.getChildren());
   ```
   
5. 自己封装hasChildren()方法，不可用children属性

   ```js
   Element.prototype.hasChildren = function (e) {
     var childNodes = e.childNodes,
         len = childNodes.length;
   	for (var i=0; i<len; i++) {
       if (childNodes[i].nodeType == 1) {
         return true;
       }
     }
     return false;
   }
   ```

   

### 增加

- `document.createElement('tag name');`
- `document.createTextNode('text');`
- `document.createComment('comment');`
- `document.createDocumentFragment();`



### 插入

- `PARENTNODE.appendChild(child);`：在`PARENTNODE`中从尾部添加一个子节点。
  
  ```js
  var text = document.createTextNode('I AM A TEXT.');
  var span = document.createElement('span');
  document.body.appendChild(text);
  document.body.appendChild(span);
  ```
  ```html
  <body>
    "I AM A TEXT."
    <span></span>
  </body>
  ```

  **注意**：

  - 对于新创建的节点，`appendChild()`方法就是把新节点插入到父节点中。
  
  - 对于已经存在的节点，`appendChild()`方法其实是把要插入的节点**剪切**到了父节点中。
  
    ```html
    <!-- 原html -->
    <strong></strong>
    <div></div>
    ```
  
    ```js
    var strong = document.getElementsByTagName('strong')[0];
    var div = document.getElementsByTagName('div')[0]；
    div.appendChild(strong);
    ```
  
    ```html
    <!-- 操作后的html -->
    <div>
      <strong></strong>
    </div>
    ```
  
- `PARENTNODE.insertBefore(a, b);`：往`PARENTNODE`中，insert `a` before `b`。

  ```html
  <!-- 原html -->
  <div>
    <strong></strong>
  </div>
  ```

  ```js
  var div = document.getElementsByTagName('div')[0];
  var strong = document.getElementsByTagName('strong')[0];
  var span = document.createElement('span');
  div.insertBefore(span, strong);
  ```

  ```html
  <!-- 操作后的html -->
  <div>
    <span></span>
    <strong></strong>
  </div>
  ```

  

### 删除

- `parent.removeChild(child);`
- `child.remove();`



### 替换（改）

> 使用的不多。

- `parent.replaceChild(new, origin);`：在`parent`中，用`new`替换`origin`。



### 练习

1. 编写一段JavaScript脚本生成下面这段DOM结构。要求：使用标准的DOM方法或属性。

  ```html
  <div class="example">
    <p class="slogan">Something</p>
  </div>
  ```

  提示：`dom.className`可以读写`class`。

  实现：

  ```js
  var div = document.createElement('div');
  var p = document.createElement('p');
  div.setAttribute('class', 'example');
  p.setAttribute('class', 'slogan');
  p.innerText = "Something";
  div.appendChild(p);
  document.body.appendChild(div);
  ```

2. 封装函数`insertAfter();`，功能类似`insertBefore();`，直接在`Element.prototype`上编程。

   ```js
   Element.prototype.insertAfter = function (target, origin) {
     if (origin.nextSibling) {
       return this.insertBefore(target, origin.nextSibling);    
     }else {
      	return this.appendChild(target);
     }
   }
   ```

3. 将目标节点内部的节点顺序逆序。

   eg. `<div><a></a><em></em></div>` —> `<div><em></em><a></a></div>` 

   提示：使用`appendChild()`方法的剪切功能。
   
   ```js
   Element.prototype.reverseChildNodes = function () {
     var childNodes = this.childNodes,
         len = childNodes.length;
     for (var i=len-1; i>=0; i--) {
       this.appendChild(childNodes[i]);
     }
}
   ```
   
   

# 日期对象 `Date()`

## 创建Date对象

```js
var date = new Date();
var date = new Date(milliseconds);
var date = new Date(dateString);
var date = new Date(year, month, day, hours, minutes, seconds, milliseconds);
```

## Date对象的常见方法

| 方法                                                         | 描述                                        |
| :----------------------------------------------------------- | :------------------------------------------ |
| [getDate()](http://www.w3school.com.cn/jsref/jsref_getDate.asp) | 从 Date 对象返回一个月中的某一天 (1 ~ 31)。 |
| [getDay()](http://www.w3school.com.cn/jsref/jsref_getDay.asp) | 从 Date 对象返回一周中的某一天 (0 ~ 6)。    |
| [getMonth()](http://www.w3school.com.cn/jsref/jsref_getMonth.asp) | 从 Date 对象返回月份 (0 ~ 11)。             |
| [getFullYear()](http://www.w3school.com.cn/jsref/jsref_getFullYear.asp) | 从 Date 对象以四位数字返回年份。            |
| [getYear()](http://www.w3school.com.cn/jsref/jsref_getYear.asp) | 请使用 getFullYear() 方法代替。             |
| [getHours()](http://www.w3school.com.cn/jsref/jsref_getHours.asp) | 返回 Date 对象的小时 (0 ~ 23)。             |
| [getMinutes()](http://www.w3school.com.cn/jsref/jsref_getMinutes.asp) | 返回 Date 对象的分钟 (0 ~ 59)。             |
| [getSeconds()](http://www.w3school.com.cn/jsref/jsref_getSeconds.asp) | 返回 Date 对象的秒数 (0 ~ 59)。             |
| [getMilliseconds()](http://www.w3school.com.cn/jsref/jsref_getMilliseconds.asp) | 返回 Date 对象的毫秒(0 ~ 999)。             |
| **[getTime()](http://www.w3school.com.cn/jsref/jsref_getTime.asp)** | **返回 1970 年 1 月 1 日至今的毫秒数。**    |

```js
// 使用 getTime() 方法求时间差
var firstTime = new Date().getTime();
for (var i=0; i<1000000; i++) {}
var lastTime = new Date().getTime();
console.log("耗时", lastTime - firstTime, "毫秒");
// 耗时 4 毫秒
```

| 方法                                                         | 描述                                  |
| ------------------------------------------------------------ | ------------------------------------- |
| [setDate()](http://www.w3school.com.cn/jsref/jsref_setDate.asp) | 设置 Date 对象中月的某一天 (1 ~ 31)。 |
| [setMonth()](http://www.w3school.com.cn/jsref/jsref_setMonth.asp) | 设置 Date 对象中月份 (0 ~ 11)。       |
| [setFullYear()](http://www.w3school.com.cn/jsref/jsref_setFullYear.asp) | 设置 Date 对象中的年份（四位数字）。  |
| [setYear()](http://www.w3school.com.cn/jsref/jsref_setYear.asp) | 请使用 setFullYear() 方法代替。       |
| [setHours()](http://www.w3school.com.cn/jsref/jsref_setHours.asp) | 设置 Date 对象中的小时 (0 ~ 23)。     |
| [setMinutes()](http://www.w3school.com.cn/jsref/jsref_setMinutes.asp) | 设置 Date 对象中的分钟 (0 ~ 59)。     |
| [setSeconds()](http://www.w3school.com.cn/jsref/jsref_setSeconds.asp) | 设置 Date 对象中的秒钟 (0 ~ 59)。     |
| [setMilliseconds()](http://www.w3school.com.cn/jsref/jsref_setMilliseconds.asp) | 设置 Date 对象中的毫秒 (0 ~ 999)。    |
| [setTime()](http://www.w3school.com.cn/jsref/jsref_setTime.asp) | 以毫秒设置 Date 对象。                |



# 定时器

- `setInterval(function () {}, timeInterval);`：每过 timeInterval 毫秒之后就会执行一次函数体。

  - 该函数返回该定时器的唯一标识数字，比如如果只有一个定时器，那就返回数字1。
  - 使用`clearInterval(timer)`，可以终止该定时器的运行，参数`timer`是该定时器的唯一标识数字。

  ```js
  var i = 0;
  var timer = setInterval(function () {
    console.log(i++);
    if (i > 10) {
      clearInterval(timer);
    }
  }, 1000)
  // 0 1 2 3 4 5 6 7 8 9 10
  ```

- `setTimeout(function () {}, timeInterval);`：经过 timeInterval 毫秒之后（只）执行一次函数体。

  - 同样该函数也会返回该定时器的唯一标识数字，并且与`setInterval`生成的定时器的标识数字不会重复。
  - 一般情况下不需要清除`setTimeout`定时器，但是如果需要取消`setTimeout`定时器的运行，可以通过`clearTimeout(timer)`来清除该定时器，同样传入的参数`timer`是该定时器的唯一标识数字。

  ```js
  var timer = setTimeout(function () {
    console.log('timeout')
  }, 5000);
  ```

> 注意：
>
> 上述方法都是全局对象 `window` 上的方法，内部函数中的 `this` 指向 `window`



# 小知识点

## 获取窗口属性

- 查看滚动条的滚动距离

  - `window.pageXOffset` `window.pageYOffset`

    - IE8及IE8以下不兼容

  - `document.body.scrollLeft` `document.body.scrollTop`

    `document.documentElement.scrollLeft` `document.documentElement.scrollTop`

    - 这两个方法的IE兼容性比较混乱，但是二者互斥，即当`document.body.scrollLeft`有值时，`document.documentElement.scrollLeft`不可能有值。因此，在使用过程中，通常将二者相加，来解决IE的兼容性混乱的问题。

  **练习**：封装兼容性方法，求滚动轮滚动距离 `getScrollOffset()`

  ```js
  function getScrollOffset () {
    if (window.pageXOffset) {
      return {
        x: window.pageXOffset,
        y: window.pageYOffset
      }
    }else {
      return {
        x: document.body.scrollLeft + document.documentElement.scrollLeft,
        y: document.body.scrollTop + document.documentElement.scrollTop
      }
    }
  }
  ```

- 求可视区窗口（视口）的尺寸

  -  `window.innerWidth` `window.innerHeight`
    - IE8及IE8以下不兼容
  - `document.documentElement.clientWidth` `document.documentElement.clientHeight`
    - 标准模式下，任意浏览器都兼容
  - `document.body.clientWidth` `document.body.clientHeight`
    - 适用于怪异模式（或称混杂模式）下的浏览器
    - 使用`document.compatMode`可以查看是标准模式还是怪异模式
      - `"CSS1Compat"`—>标准模式
      - `"BackCompat"`—>怪异模式：使用老版本的模式来执行代码

  **练习**：封装兼容性方法，返回浏览器视口尺寸 `getViewportSize()`

  ```js
  function getViewportSize() {
      if (window.innerWidth) {
          return {
          width: window.innerWidth,
          height: window.innerHeight
          }
      }else {
          var compatMode = document.compatMode;
          if (compatMode == "CSS1Compat") {
              return {
              width: document.documentElement.clientWidth,
              height: document.documentElement.clientHeight
              }
          }else {
              return {
              width: document.body.clientWidth,
              height: document.body.clientHeight
              }
          }
      }
  }
  ```



## 获取DOM属性

- `domElem.getBoudingClientRect();`

  - 不常用
  - 兼容性良好
  - 该方法返回一个对象，对象里面有`left` `top`  `right` `bottom`等属性。
    - `left` `top` 代表该元素左上角的 x y 坐标
    - `right` `bottom` 代表该元素右下角的 x y 坐标
    - `height` `width` 属性老版本IE并未实现
    - 返回的结果并不是“实时的”

- `domElem.offsetWidth`, `domElem.offsetHeight`：查看元素的尺寸

  - 获取的是dom的视觉上的尺寸，包含border padding content部分。 

- `domElem.offsetLeft`, `domElem.offsetTop`：查看元素位置

  - 其返回的结果是: 
    - 如果该dom元素无**有定位的父级元素**，则返回相对文档的相对位置
    - 反之，返回距离该dom元素最近的**有定位的父级元素**的相对位置。

  - **实例**：

    ```html
    <div style="width:300px; height:300px; border:1px solid black; position:relative">
      <div class="innerDiv1" style="width:100px; height:100px; position:absolute; left:100px; top:100px; background-color:red"></div>
      <div class="innerDiv2" style="width:100px; height:100px; position: absolute; margin-left:100px; margin-top:100px; border:2px solid blue"></div>
    </div>
    ```

    <div style="width:300px; height:300px; border:1px solid black; position:relative">
      <div class="innerDiv1" style="width:100px; height:100px; position:absolute; left:100px; top:100px; background-color:red"></div>
      <div class="innerDiv2" style="width:100px; height:100px; position: absolute; margin-left:100px; margin-top:100px; border:2px solid blue"></div>
    </div>

    ```js
    var innerDiv1 = document.getElementsByClassName('innerDiv1')[0],
        innerDiv2 = docuemtn.getElementsByClassName('innerDiv2')[0];
    console.log(innerDiv1.offsetLeft, innerDiv1.offsetTop);
    // 100 100
    console.log(innerDiv2.offsetLeft, innerDiv2.offsetTop);
    // 100 100
    ```

- `dom.offsetParent`：返回最近的有定位的父级，如无，返回body。

  - `body.offsetParent`是`null`。
  
    

**练习**：求元素相对于文档的坐标 `getElementPosition`

```js
Element.prototype.getElementPosition() = function () {
    var left = this.offsetLeft,
        top = this.offsetTop,
        elem = this;
    while (elem.offsetParent) {
        elem = elem.offsetParent;
        left += elem.offsetLeft;
        top += elem.offsetTop;
    }
    return {
        left: left,
        top: top,
      }
  }
```

  

## 让滚动条滚动

`window`上有三个方法

- `scroll(x, y)`/`scrollTo(x, y)`：让滚动条滚动到坐标`(x, y)`的位置
- `scrollBy(x_dist, y_dist)`：让滚动条横向滚动`x_dist`，纵向滚动`y_dist`距离

**练习**：利用`scrollBy()`实现快速阅读的功能

```js
var startNode = document.getElementsByClassName('start')[0];
var stopNode = document.getElementsByClassName('stop')[0];
var timer = 0,
    key = true;
startNode.onclick = function () {
    if (key) {
	    timer = setInterval(function () {
            scrollBy(0, 1);		
        }, 100);
        key = false;
    }
}
stopNode.onclick = function () {
    clearInterval(timer);
    key = true;
}
```



# 脚本化CSS

## 读写元素css属性

-   `dom.style`：表示的是dom元素的**行间样式**，是一个类数组。

-   `dom.style.prop`：dom元素的行间样式中的`prop`属性。

    -   该属性可读可写，没有兼容性问题，写入的值必须是字符串格式
    -   遇到`-`连接的css样式，使用小驼峰式命名，如：
        -   `background-color`—>`dom.style.backgroundColor`
        -   `font-size`—>`dom.style.fontSize`
    -   复合样式拆开尽量拆开来写，如：
        -   [建议]：
            -   `dom.style.borderWidth = “1px”`
            -   `dom.style.borderStyle = “solid”`
            -   `dom.style.borderColor = “black”`
        -   [不建议]：`dom.style.border = “1px solid black”`
    -   遇到`float`这样的保留字属性，前面应该加`css`，如：
        -   `dom.style.cssFloat`
    -   只能读取行间样式，如果`prop`是非行间样式，则`dom.style.prop`为空串`""`

    ```html
    <div style="height: 100px"></div>
    <style>
        div {
            width: 100px;
        }
    </style>
    <script>
    	var div = document.getElementsByTagName('div')[0];
        console.log(div.height);	// "100px"
        console.log(div.width);		// ""
    </script>
    ```

    

## 查询计算样式

-   `window.getComputedStyle(dom, null)`：返回dom**元素最终可视的样式表**，是一个类似数组

    -   IE8及IE8以下不兼容
    -   该函数的第二个参数是为了获取伪元素的属性的
        -   如果是`null`，那么就获取dom元素的样式表
        -   如果是其他值，如`"after"`，那么就会获取dom元素对应伪元素`dom::after`的样式表

-   `window.getComputedStyle(dom, null).prop`：返回dom元素某属性的计算样式

    -   IE8及IE8以下不兼容

    -   该属性是只读的，不可写入

    -   返回的计算样式的值都是绝对值，没有相对单位，比如：

        ```html
        <div style="height: 100px"></div>
        <style>
            div {
                width: 10em;
                background-color: "red";
            }
        </style>
        <script>
        	var div = document.getElementsByTagName('div')[0];
            console.log(getComputedStyle(div, null).height);	// "100px"
            console.log(getComputedStyle(div, null).width);		// "160px"
            console.log(getComputedStyle(div, null).backgroundColor);	// rgb(255, 0, 0)
        </script>
        ```

        其中，

        -   `getComputedStyle(div, null).width`是`"160px"`是因为：em是相对单位，1em默认值是16px，因此10em为160px，而由于返回的式计算样式，因此返回的值是计算后的绝对值，即`"160px"`。
        -   `getComputedStyle(div, null).backgroundColor`是`"rgb(255, 0, 0)"`是因为经过计算后，颜色值使用的是rgb模式。

-   `elem.currentStyle`：IE独有的属性，可用于查询元素的计算样式

    -   只读，不可写入
    -   返回的计算样式的值**不是**经过转换的绝对值，因此css中赋的什么值，不管是否是相对单位，都会返回赋的值



**练习**：封装兼容性方法 `getStyle(elem, prop)`

```js
function getStyle(elem, prop) {
    if (window.getComputedStyle(elem, null)) {
        return window.getComputedStyle(elem, null)[prop];
    }else {
        return elem.crrentStyle[prop];
    }
}
```



# 事件

## 绑定事件的3种方式

-   `dom.on+事件类型 = function (event) {}`

    ```js
    var div = document.getElementsByTagName('div')[0];
    div.onclick = function (e) {
        console.log("I am clicked!");
    }
    ```

    -   兼容性很好，但是一个元素的同一个事件上只能绑定一个处理函数
    -   基本等同于写在HTML行间上

-   `dom.addEventListener(事件类型, func, false)`

    ```js
    var div = document.getElementsByTagName('div')[0];
    function print() {
        cosole.log("I am clicked!");
    }
    div.addEventListener("click", print, false);
    ```

    -   IE9以下不兼容，可以为一个事件绑定多个处理函数
    -   一个事件绑定同一个处理函数多次，只执行一次

-   `dom.attachEvent('on'+事件类型, func)`

    -   IE独有的方法，可以为一个事件绑定多个处理函数
    -   一个事件板绑定一个处理函数多次，执行多次



**练习**：给下面的`li`标签使用`addEventListener`绑定事件，输出其顺序。

```html
<ul>
    <li>a</li>
    <li>a</li>
    <li>a</li>
    <li>a</li>
</ul>
```

错误代码：

```js
var li = document.getElementsByTagName('li'),
    len = li.length;
for (var i=0; i<len; i++) {
    li[i].addEventListener("click", function () {
        console.log(i);
    }, false);
}
```

错误原因：

-   给`li`标签绑定事件的时候，形成了闭包，因此无论点击哪个标签，只会输出`4`。

正确代码：

-   使用立即执行函数解决闭包问题。

```js
var li = document.getElementsByTagName('li'),
    len = li.length;
for (var i=0; i<len; i++) {
    (function (j) {
        li[j].addEventListener("click", funciton () {
       		console.log(j);                       
       }, false);
    } (i));
}
```



## 绑定事件的运行环境

-   `dom.on+事件类型 = function () {}`：程序this指向为dom元素本身

-   `dom.addEventListener(事件类型, func, false)`：程序this指向为dom元素本身

-   `dom.attachEvent(on+事件类型, func)`：程序this指向为window

    -   为了让事件处理函数中的`this`指向dom元素本身，可以使用`call`或者`apply`的方法，如：

        ```js
        var div = document.getElementsByTagName('div')[0];
        div.attachEvent("onclick", function (e) {
            handle.call(div);
            // 或 handle.apply(div, arguments);
        });
        function handle (e) {
            console.log(this);
        }
        ```

**练习**：封装兼容性的`addEvent(elem, type, handle)`方法

```js
function addEvent(elem, type, handle) {
    if (elem.addEventListener) {
        elem.addEventListener(type, handle, false);
    }else if (elem.attachEvent) {
        elem.attachEvent(on+type, function () {
            handle.call(elem);
        });
    }else {
        elem['on' + type] = handle;
    }
}
```



## 事件解除处理函数

-   `dom.onclick = false/null/""`
-   `dom.removeEventListener(事件类型, 事件处理函数, false)`
-   `dom.detachEvent(on+事件类型, 事件处理函数)`

**注意**：第二、三种方式中，若绑定匿名函数，则无法解除



## 事件处理模型

-   事件冒泡
    -   结构上（非视觉上）嵌套关系的元素，会存在事件冒泡的功能，即同一个事件，自子元素冒泡向父元素。（自底向上）
    -   取消冒泡的方法：
        -   `event.stopPropagation()`：W3C标准，即不支持IE9以下版本
        -   `event.cancelBubble = true;`：IE和谷歌都可以使用

-   捕获

    -   结构上（非视觉上）嵌套关系的元素，会存在事件捕获的功能，即同一个事件，子父元素捕获至子元素（事件源元素）。（自顶向下）

    -   开启方法：

        `dom.addEventListener(事件类型, func, true)`

    -   IE没有捕获事件

-   触发顺序，先捕获，后冒泡

-   focus blur change submit reset select 等事件不冒泡

-   阻止默认事件：

    -   默认事件——表单提交，a标签跳转，右键菜单等

    -   方法：

        -   `return false` 以对象属性的方式注册的事件才生效，比如：

        -   `event.preventDefault()`：W3C标准，IE9以下不兼容

        -   `event.returnValue = false`：兼容IE
          
            ```js
            // document.oncontextmenu 默认右键菜单事件
            document.oncontextmenu = function (e) {
                return false;
                // e.preventDefault();
                // e.returnValue = false;
            }
            ```
        

        **练习** ：封装兼容性的阻止默认事件的函数 `cancelHandler(event)`

        ```js
        function cancelHandle(event) {
            if (event.preventDefault) {
        event.preventDefault();
            }else {
                event.returnValue = false;
            }
        }
        ```
        
        **a标签取消默认跳转的常用方法**：
        
        ```html
        <a href="javascript:void(false)">demo</a>
        ```
        
        `href`中可以写js代码，`void(xxx)`则相当于`return xxx`，因此等同于：
        
        ```js
        var a = document.getElementsByTagName('a')[0];
        a.onclick = function (e) {
            return false;
        }
        ```
        
        

## 事件对象

### 事件对象

事件对象会将事件发生时的各类参数作为属性存储在对象中。

兼容性的写法：

```js
function handle(event) {
    var event = event || window.event;
}
```

其中，`window.event`是IE的写法

### 事件源对象

触发事件的源对象，事件源对象储存在事件对象中，可以通过下面的方式获取：

-   `event.target` 火狐只有这个
-   `event.srcElement` IE只有这个
-   Chrome两个都有

兼容性的写法：

```js
var target = event.target || event.srcElement;
```



## 事件委托

利用事件冒泡，和事件源对象进行处理，将本该在子元素上绑定的事件，绑定给其父元素。

优点：

-   性能：不需要循环所有的子元素一个一个绑定
-   灵活：当有新的子元素时不需要重新绑定事件

**例子**：有十个`li`标签，绑定事件使得点击哪个`li`标签就显示该标签的内容。

```html
<ul>
    <li>1</li>
    <li>2</li>
    <li>3</li>
    <li>4</li>
    <li>5</li>
    <li>6</li>
    <li>7</li>
    <li>8</li>
    <li>9</li>
    <li>10</li>
</ul>
```

由于事件冒泡，点击`li`标签的事件会冒泡至其父元素`ul`标签，因此，与其对每个`li`标签都绑定一个事件，不如只对其父元素绑定一个事件，然后通过触发父级元素的事件对象中获取事件源对象，即究竟点的是哪个子元素。代码如下：

```js
var ul = document.getElementsByTagName('ul')[0];
ul.onclick = function (event) {
    var event = event || window.event;
    var target = event.target || event.srcElement;
    console.log(target.innerText);
}
```



**练习**：拖拽小方块

```html
<div class="box" style="width:100px;height:100px;background-color:#f00;position:absolute;left:0;right:0"></div>
```

```js
function drag(elem) {
    elem.addEventListener("mousedown", function (e) {
        var event = e || window.event;
        var dx = e.pageX - parseInt(elem.style.left),
            dy = e.pageY - parseInt(elem.style.top);
        function mouseMove(e) {
            var evnet = e || window.event;
            elem.style.left = e.pageX - dx + "px";
            elem.style.top = e.pageY - dy + "px";
        }
        function mouseUp(e) {
            document.removeEventListener('mousemove', mouseMove, false);
        }
        document.addEventListener('mousemove', mouseMove, false);
        document.addEventListener('mouseup', mouseUp, false);
    }, false);
}
```



## 鼠标事件

| 鼠标事件                | 含义                                          |
| ----------------------- | --------------------------------------------- |
| `click`                 | 鼠标左键点击事件，相当于`mousedown`+`mouseup` |
| `mousedown`             | 按下鼠标事件，`event.button`可以监听左右键    |
| `mouseup`               | 抬起鼠标事件，`event.button`可以监听左右键    |
| `mousemove`             | 鼠标移动事件                                  |
| `contextmenu`           | 鼠标右键菜单事件                              |
| `mouserover/mouseenter` | 鼠标进入事件                                  |
| `mouseout/mouseleave`   | 鼠标移出事件                                  |

注意：

-   `mousedown`和`mouseup`事件可以监听按下鼠标的哪个键，储存在其事件对象`event`中
    -   `event.button` 值为0：鼠标左键
    -   `event.button`值为1：鼠标滚轮
    -   `event.button`值为2：鼠标右键
-   DOM3标准规定：`click`事件只能监听鼠标左键

 

**练习**：如何区分`mousedown/mouseup`和`click`事件

Hint: 使用时间差，如果时间差小于300毫秒，为click，反之为`mousedown/mouseup`

```js
var firstTime = 0,
    lastTime = 0,
    key = true;		// 表示click的锁，为true表示是点击事件
document.onmousedown = function (e) {
    firstTime = new Date().getTime();
}
document.onmouseup = function (e) {
    lastTime = new Date().getTime();
    if (lastTime - firstTime > 300) {
        console.log("it's mousedown and mouseup.");
        key = false;
    }else {
        key = true;
    }
}
document.onclick = function (e) {
    if (key) {
        console.log("it's clicking.");
    }
}
```



## 键盘事件

| 键盘事件   | 含义       |
| ---------- | ---------- |
| `keypress` | 敲击键事件 |
| `keydown`  | 按下键事件 |
| `keyup`    | 抬起键事件 |

注意：

-   速度：`keydown`>`keypress`>`keyup`
-   `keydown`和`keypress`的区别
    -   `keydown`可以相应任意键盘按键（`fn`键除外，属于辅助键），`keypress`只可以响应字符类型键盘按键
    -   `keypress`的事件对象中`event.charCode`返回ASCII码，可以转化为相应的字符，而`keydown`和`keyup`的事件对象中`event.charCode`返回0。
        -   使用`String.fromCharCode(unicode编码)`可以转换成相应字符



## 文本事件

`input`标签有的事件

| 文本事件 | 含义                                                         |
| -------- | ------------------------------------------------------------ |
| `input`  | 输入事件，只要`input`标签中的文本发生变化就会触发该事件      |
| `change` | 内容改变事件，只有当`input`标签聚焦和失去焦点前后的内容发生变化才会触发该事件 |
| `focus`  | 聚焦事件                                                     |
| `blur`   | 失去焦点事件                                                 |

**练习**：一个`input`标签，未聚焦时有`“请输入用户名”`的`value`，聚焦时内容消失，且输入文本之后失去焦点时不再产生`“请输入用户名”`的`value`。

```html
<input type="text" value="请输入用户名" onfocus="if (this.value=='请输入用户名'){this.value=""}" onblur="if (this.value==""){this.value='请输入用户名'}"></input>
```



## 窗体操作类事件（`window`上的事件）

-   `scroll`：滚动事件，窗体滚动时触发的定位
-   `load`：网页渲染以及所有文件下载完之后触发的事件。



# JSON

JSON是一种传输数据的格式。

-   以对象为样板，本质上就是对象
-   与对象有用途上的区别，对象就是本地用的，json是用来传输用的

json数据的转化：

-   `JSON.parse()`：string—>json
-   `JSON.stringify()`：json—>string

```js
var obj = {
    "name": "tish",
    "age": 18
}

var str = JSON.stringify(obj);
console.log(str);
// "{"name":"tish","age":18}"

var json = JSON.parse(str);
console.log(json);
// Object {name: "tish", age: 18}
```

 

# 异步加载js

js加载的缺点

-   加载“工具方法”时没必要阻塞文档，否则会影响页面效率，一旦网速不好，整个网页将等待js加载而不进行后续的渲染工作。

有些工具方法需要按需加载，用到再加载，不用不加载。



## js异步加载的三种方法

-   defer 异步加载，

    -   等到dom文档全部解析完才会被执行
    -   只有IE能用
    -   也可以将代码写到`<script>`标签的内部

    ```html
    <script type="text/javascript" src="tools.js" defer="defer"></script>
    ```

    标签中属性名和属性值一样时，可以只写属性名，系统会自动识别，因此可以写成：

    ```html
    <script type="text/javascript" src="tools.js" defer></script>
    ```

-   async 异步加载

    -   加载完就执行
    -   IE9以下不能用
    -   async只能加载外部脚本，不能把js写在`<script>`标签里面

    ```html
    <script type="text/javascript" src="tools.js" async></script>
    ```

-   动态创建script

    -   **最常用的，兼容性最好**

    ```js
    function loadScript(url, callback) {
        var script = document.createElement('script');
        script.type = "text/javascript";
        
        // 等待js文件加载完毕之后执行其中的callback方法
        if (script.readyState) {	// IE的方法
            script.onreadystatechange = function () {
                if (script.readyState == "complete" || script.readyState == "loaded") {
                    eval(callback)();
                }
            }
        }else {	// 除IE以外的其他浏览器的方法
            script.onload = function () {
    			eval(callback)();
            }
        }
        
        // 给script.src赋值，系统就开始开另一个线程进行异步下载
        script.src = url;  
        document.head.appendChild(script);
    }
    
    loadScript("tools.js", "test");
    ```

    

##  js加载时间线

1.  创建Document对象，开始解析web页面。解析HTML元素和他们的文本内容然后添加Element对象和Text节点到文档中。这个阶段`document.readyState = ‘loading’`。
2.  遇到link外部css，创建线程加载，并继续解析文档。
3.  遇到script外部js，并且没有设置async defer，浏览器加载，并阻塞，等待js加载完成并执行该脚本，然后继续解析文档。
4.  遇到script外部js，并且设有async defer，浏览器创建线程加载，并继续解析文档。对于async属性的脚本，脚本加载完之后立即执行。（异步禁止使用`document.write()`）
5.  遇到img等，先正常解析dom结构，然后浏览器异步加载src，并继续解析文档。
6.  当文档解析完成，`document.readyState = 'interactive'`。
7.  文档解析完成后，所有设置为defer的script按照顺序执行。
8.  文档解析完成后，document对象触发`DOMContentLoaded`事件，标志着程序执行从同步脚本执行阶段，转化为事件驱动阶段。
9.  当所有async脚本加载完成并执行后，img等加载完成后，`document.readyState = 'complete'/'loaded'`，window对象触发`load`事件
10.  从此，以异步响应方式处理用户输入、网络事件等。



# 正则表达式

## 课前补充

-   转义符：`\`

-   多行字符串：通过`\`将换行符转义掉来实现多行字符串。

    ```js
    "\
    <div></div>\
    <span></span>\
    "
    ```

## RegExp

正则表达式的作用：匹配特殊字符或有特殊搭配原则的字符的最佳选择。

-   两种创建方式：
    -   直接量（**推荐**）：`/regular expression/`
    -   `new RegExp("regular expression");`

具体的用法以及规则：[W3school RegExp对象](http://www.w3school.com.cn/js/jsref_obj_regexp.asp)

**练习**：将`the-first-name`使用正则表达式替换为小驼峰式，即`theFirstName`

```js
var str = "the-first-name";
var reg = /-(\w)/g;
console.log(str.replace(reg, function ($, $1) {
    return $1.toUpperCase();
}));
// theFirstName
```

