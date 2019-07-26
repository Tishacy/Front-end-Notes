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



### 避免使用缓存数据

在IE浏览器中使用AJAX时，如果GET请求的地址url如果是一样的话，那么只会请求一份，其余的从缓存中读取，如果url文件的内容发生变化，那么使用AJAX请求的数据并不会实时更新，为了避免使用缓存数据，可以通过增加随机数参数或者时间参数来每次构造不同的url进行请求。

```js
xmlhttp.open("GET", `${url}?r=${Math.random()}`, true);
// 或者
xmlhttp.open("GET", `${url}?t=${new Date().getTime()}`, true);
```



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



# 封装AJAX

## 基本封装

```js
function json2str(json) {
    /** 将json数据转化为字符串格式
    * 
    * @param {object} json 传入的json数据
    * @return {string} str 传出的字符串数据，格式为'key1=value1&key2=value2'
    */
    json.t = new Date().getTime();
    let res = [];
	for (let key in json) {
        // url中不能出现中文，因此需要转一下编码
        key = encodeURIComponent(key);
        val = encodeURLComponent(json[key]);
        res.push(`${key}=${val}`);
    }
    return res.join("&");
}


function ajax(method, url, data, timeout, succes, error) {
    /** AJAX by GET method
     * 使用GET方法发送AJAX请求，请求成功时执行success回调函数，请求失败时执行error回调函数。
     * 
     * @param {string} method 请求方式，"GET"或"POST"
     * @param {string} url 请求地址
     * @param {object} data 请求的数据，json格式
     * @param {int} timeout 设置超时时间 
     * @param {function} success GET请求成功时的回调函数
     * @param {function} error GET请求失败时的回调函数
     */
    let dataStr = json2str(data);
    let timer;
    let xmlhttp = (window.XMLHttpRequest)? new XMLHttpRequest() : new ActiveXObject("Microsoft.XMLHTTP");
    if (method.toLowerCase() === "get") {
        xmlhttp.open("GET", `${url}?${dataStr}`, true);
        xmlhttp.send();        
    }else {
        xmlhttp.open("POST", url, true);
        xmlhttp.setRequestHeader('Content-type', 'application/x-www-form-urlencoded');
        xmlhttp.send(dataStr);
    }

    xmlhttp.onreadystatechange = function (e) {
        if (xmlhttp.readyState === 4) {
            clearTimeout(timer);
            if (xmlhttp.status >= 200 && xmlhttp.status < 300 || xmlhttp.status === 304) {
                succes(xmlhttp);
            }else {
                error(xmlhttp);
            }
        }
    }
    if (timeout) {
        timer = setTimeout(()=>{
            console.log("中断请求");
            xmlhttp.abort();
            clearTimeout(timer);
        }, timeout);
    }
}
```

需要注意的问题：

-   将GET/POST数据由json数据转化为字符串格式，使用`json2str`函数
-   使用`Math.random()`或者`new Date().getTime()`作为GET的参数之一，来避免请求缓存数据
-   url中不能出现中文字符，因此需要通过`encodeURI`或`encodeURIComponent`函数来将url转码，但是为了避免`=`也被转码，因此需要分别将各数据参数的`key`和`value`进行转码，以及将原url进行转码，再进行拼接
-   使用`timeout`来设置AJAX请求的超时问题



## 完善封装

为了解决AJAX传参的顺序问题，将封装的`ajax`函数的参数换成参数对象。

```js
let json2str = function(data) {
    /** 将json数据转化为字符串格式
    * 
    * @param {object} json 传入的json数据
    * @return {string} str 传出的字符串数据，格式为'key1=value1&key2=value2'
    */
    let res = [];
    for (let key in data) {
        key = encodeURI(key);
        let val = encodeURI(data[key]);
        res.push(`${key}=${val}`);
    }
    return res.join("&");
}

let ajax = function(options) {
    /** ajax function
     *
     * @param {object} options 参数对象
     *     options = {
     *         @param {string} method 请求方式，"GET"或“POST”
     *         @param {string} url 请求地址
     *         @param {string} data 请求数据
     *         @param {object} headers 请求头
     *         @param {number} timeout 请求超时时间
     *         @param {function} success 请求成功回调函数
     *         @param {function} error 请求失败会滴啊函数
     *     }
     */
    let timer;
    let xhr = (window.XMLHttpRequest)? new XMLHttpRequest() : new ActiveXObject("Microsoft.XMLHTTP");
    if (options.method.toLowerCase() === "get") {
        url = (options.data)? `${options.url}?${options.data}` : options.url;
        xhr.open("GET", url, true);
        xhr.send();
    }else {
        xhr.open("POST", `${options.url}`, true);
        for (let header in options.headers) {
            xhr.setRequestHeader(header, options.headers[header]);
        }
        xhr.send(options.data);
    }
    xhr.onreadystatechange = function (e) {
        if (xhr.readyState === 4) {
            clearTimeout(timer);
            if (xhr.status >= 200 && xhr.status < 300 || xhr.status === 304) {
                options.success(xhr);
            }else {
                options.error(xhr);
            }
        }
    }
    if (options.timeout) {
        timer = setTimeout(()=>{
            xhr.abort();
            clearTimeout(timer);
        }, options.timeout);
    }
}

```

使用方法：

```js
ajax({
    type: "GET",
    url: "ajax.php",
    data: "userName=ss&userPwd=123456",
    success: function(xhr) {
        console.log(xhr.status);
    },
    error: function(xhr) {
        console.log(xhr.status);
    }
})
```



# 使用Jquery发送AJAX

```html
<script src="http://code.jquery.com/jquery-latest.js"></script>
<script>
	$.ajax({
        type: "GET",
        url: "ajax.php",
        data: "userName=ss&userPwd=123456",
        success: function(msg) {
            console.log(msg);
        },
        error: function(xhr) {
            console.log(xhr.status);
        }
    })
</script>
```



