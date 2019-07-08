<h1 style="fontsize:28px;text-align:center;font-family:Georgia;font-style:italic;color:#333">DOM Learning Notes</h1>
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

- `element.setAttribute();`
- `element.getAttribute();`

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

    



