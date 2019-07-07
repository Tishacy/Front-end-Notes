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



### 查

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



# 练习

1. 遍历元素节点树，在原型链上编程

   ```js
   Node.prototype.getChildren = function (){
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

2. 封装函数，返回元素e的第n层祖先元素节点

   ```js
   function getParent(e, n) {
       return (e == null)? null : ((n>0)? getParent(e.parentElement, n-1) : e);
   };
   
   // 另一种写法
   function getParent(e, n) {
     if (e == null | n == 0) {
       return e;
     }else if (n > 0) {
       return getParent(e.parentElement, n-1);
   }
   ```

3. 封装函数，返回元素e的第n个兄弟元素节点，n为正，返回后面的兄弟元素节点，n为负，返回前面的，n为0，返回自身。

   ```js
   function getSibling(e, n) {
       if (n == 0 | e == null) {
           return e;
       }else if (n < 0) {
           return getSibling(e.previousElementSibling, n+1);
       }else if (n > 0) {
           return getSibling(e.nextElementSibling, n-1);
       }
   }
   ```

4. 自己封装hasChildren()方法，不可用children属性

   ```js
   function hasChildren(e) {
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

   

