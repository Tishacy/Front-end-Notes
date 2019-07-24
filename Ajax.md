<h1 style="fontsize:28px;text-align:center;font-family:Georgia;font-style:italic;color:#333">AJAX</h1>
[TOC]

# 什么是AJAX

-   AJAX：Asynchronous JavaScript and XML （异步JavaScript和XML）
-   AJAX不是新的编程语言，而是一种使用现有标准的新方法
-   AJAX即在不重载整个页面的情况下，与服务器交换数据并更新部分网页



# 如何使用AJAX

使用AJAX需要下面5个步骤：

1.  创建一个异步对象
2.  设置请求方式和请求地址
3.  发送请求
4.  监听状态的变化
5.  获取服务器响应并处理响应信息



## 1. 创建XMLHttpRequest对象

-   IE7及其以上，其他所有浏览器：

    ```js
    let variable = new XMLHttpRequest();
    ```

-   IE5和IE6：

    ```js
    let variable = new ActiveXObject("Microsoft.XMLHTTP");
    ```

-   兼容性写法：

    ```js
    let xmlhttp = (window.XMLHttpRequest)? new XMLHttpRequest() : new ActiveXObject("Microsoft.XMLHTTP");
    ```



## 2. 设置请求方式和请求地址

```js
// let xmlhttp = new XMLHttpRequest();
xmlhttp.open(method, url, async);
```

-   `method`：请求的类型：GET 或 POST
-   `url`：文件在服务器上的位置
-   `async`：`true`为异步，`false`为同步，**几乎所有的情况下都是用`true`异步方法**



### 使用GET还是POST?

与POST相比，GET更简单也更快，并且在大部分情况下都能用。

然而，在以下情况下，请使用POST请求：

-   无法使用缓存文件（更新服务器上的文件或数据库）
-   向服务器发送大量数据（POST没有数量限制）
-   发送包含未知字符的用户输入时，POST比GET更稳定也更可靠



## 3. 发送请求

### 发送GET请求

```js
// let xmlhttp = new XMLHttpRequest();
// xmlhttp.open("GET", "https://www.google.com", true);
xmlhttp.send();
```

### 发送POST请求

```js
// let xmlhttp = new XMLHttpRequest();
// xmlhttp.open("POST", "http://www.google.com", true);
// 设置请求头(optional)
xmlhttp.setRequestHeader(header, value)
xmlhttp.send(string);
```

-   设置请求头：`xmlhttp.setRequestHeader(header, value)`
    -   `header`：请求头名，如`Content-type` `Accept-Language` `Cookie`等
    -   `value`：请求头值，如`text/htmL`等
-   发送请求：`xmlhttp.send(string)`
    -   `string`：POST请求的数据，如`"username=tish&password=123456"`



## 4. 监听状态的变化

-   `xmlhttp.onreadystatechagne`事件：当`xmlhttp.readyState`发生改变时就会调用该回调函数
-   `xmlhttp.readyState`：(int) XMLHttpRequest的状态
    -   0：请求未初始化
    -   1： 服务器连接已建立
    -   2：请求已接收
    -   3：请求处理中
    -   4：请求已完成，且响应已就绪
-   `xmlhttp.status`：HTTP状态码，以下为常用状态码：
    -   200：”OK“
    -   大于等于200且小于300，或者等于304，均表示接收响应成功
    -   404：未找到页面

**实例**

```js
// let xmlhttp = new XMLHttpRequest();
// xmlhttp.open("GET", "https://www.google.com", true);
// xmlhttp.send();
xmlhttp.onreadystatechange = function() {
    if (xmlhttp.readyState === 4) {
        if (xmlhttp.status >= 200 && xmlhttp.status < 300 || xmlhttp.status === 304) {
            console.log("Already get the response.");
        }
    }
}
```



## 5. 获取服务器响应

可以使用`responseText` `responseXML`来获取服务器响应

-   `responseText`：获取字符串形式的响应数据
    -   如果返回的响应是JSON数据，那么可以通过`JSON.parse(xmlhttp.responseText)`来得到数据
-   `responseXML`：获取XML形式的相应数据
    -   获取XML格式的数据时，可以使用DOM中的方法：`xmlhttp.responseXML.getElementsByTagName('book');`



