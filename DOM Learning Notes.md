<h1 style="fontsize:28px;text-align:center;font-family:Georgia;font-style:italic;color:#333">DOM Learning Notes</h1>

[TOC]

# 什么是DOM

- DOM （Document Object Model）：文档对象模型
- DOM定义了表示和修改文档所需的方法。DOM对象即为宿主对象，由浏览器厂商定义，用来**操作html和xml功能的一类对象的集合**。
- 也有人称DOM是对html和xml的**标准编程接口**。



# DOM基本操作

## 对节点的增删改查

### 查

- document代表整个文档

  - `html`标签只是document的根标签，而不代表整个文档

- `document.getElementById('id名')`：选出id为`'id名'`的元素。

  - 由于html中的所有标签的id名不可以重复，一个id只能代表一个元素，因此这个方法得到的只是一个元素，而不是元素列表。
  - 在IE8以下的浏览器，不区分id大小写， 而且也返回匹配name属性的元素。

- `document.getElementsByTagName('tag名')`：根据标签名选择元素列表

  - 由于标签名是可以重复的，因此返回选中的元素列表。
  - 返回的元素列表不是数据，而是一个类数组。
  - **最主流的用法**

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

- `document.querySelector('css selector')`：根据css选择器来选则元素，返回一个元素。比如：

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
  - 虽然很强大，但是实际开发中不怎么用，是因为它并不同步更新。
    - 就是说如果在使用该方法获取了元素a之后，页面中的元素a发生了变化，那么获取到的元素a并不同步变化，相当于只是做了个未更改前的元素a的拷贝（类似快照）。
    - 而其他的元素查找方法则返回的是元素的引用，是可以同步变化的，因此别的查找方法更常用。

- `document.querySelectorAll('css selector')`：根据css选择器来选则元素，返回一个元素列表。比如：

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