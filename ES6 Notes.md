<h1 style="fontsize:28px;text-align:center;font-family:Georgia;font-style:italic;color:#333">ES6</h1>
[TOC]

# 声明变量

-   块级作用域:
    -   凡是遇到`{}`的时候，`{}`内部的作用域称之为块级作用域
    -   比如：`if (){/*xxx*/}` `for (){/*xxx*/}` `while () {/*xxx*/}` `function () {/*xxx*/}`
    -   块级作用域存在父子结构时，子作用域可以访问父级作用域的变量。
-   在没有块级作用域的时候，可以使用立即执行函数来模拟块级作用域。



## 变量声明的方法

### `let`

#### `let`的使用方法

`let`的使用方法与`var`一致：

```js
let a = 123;
let b;
b = 345;
```



#### `let`的一些特性

-   `let`声明的变量不会进行预编译，即不进行变量提升。
-   在同级作用域下，`let`不能对已经声明过的变量进行声明，否则会报错。
    -   如果在非同级作用域下，比如父子级作用域，或者平行级作用域是可以重复声明的。
-   `let`声明的变量，只在其对应的块级作用域内部可以访问，外部访问时会报错。



详细说明：

-   `let`声明的变量不会进行预编译，即不进行变量提升。

    -   因此，在`let`进行变量声明并赋值之前使用变量就会报错，这种访问不到变量的现象称为**Temporal Dead Zone** (TDZ)，即”**暂时死区**“。

    ```js
    function fn() {
        console.log(a);		// Temporal Dead Zone (TDZ)
        let a = 123;
    }
    fn();
    // Uncaught ReferenceError: Cannot access 'a' before initialization
    ```

-   在同级作用域下，`let`不能对已经声明过的变量进行声明，否则会报错。比如：

    ```js
    var a = 123;
    let a = 345;
    // Uncaught SyntaxError: Identifier 'a' has already been declared
    ```
    
    ```js
    let a = 123;
    let a = 456;
    // Uncaught SyntaxError: Identifier 'a' has already been declared
    ```
    
    -   如果在非同级作用域下，比如父子级作用域，或者平行级作用域是可以重复声明的。
    
        ```js
        // 父子级作用域
        {
            let a = 123;
            {
                let a = 234;
                console.log(a);
            }
        }
        // 234
        ```
    
        ```js
        // 平行级作用域
        {
            let a = 123;
        }
        {
            let a = 234;
            console.log(a);
        }
        // 234
        ```
    
    注意：`for (){}`中，`()`与`{}`是父子级作用域，因此可以重复声明
    
    ```js
    for (let i=0; i<10; i++) {
        let i = 123;
        console.log(i);
    }
    // 123 x10次
    ```
    
-   `let`声明的变量，只在其对应的块级作用域内部可以访问，外部访问时会报错。

    ```js
    function fn() {
        var a = 123;
        let b = 234;
    }
    console.log(a);	// 123
    console.log(b); // Uncaught ReferenceError: b is not defined
    ```
    
    ```js
    for (let i = 0; i < 5; i++) {}
    console.log(i);
    // Uncaught ReferenceError: i is not defined
    ```

**例子**：解决下面代码的闭包问题

```js
var arr = [];
for (var i=0; i<3; i++){
    arr[i] = function () {
        console.log(i);
    }
}
arr[0]();	// 3
arr[1]();	// 3
arr[2]();	// 3
```

-   方法1：使用立即执行函数来解决闭包问题

    ```js
    var arr = [];
    for (var i=0; i<3; i++) {
        (function (j) {
            arr[j] = function () {
                console.log(j);
            }
        })(i);
    }
    arr[0]();	// 0
    arr[1]();	// 1
    arr[2]();	// 2
    ```

- 方法2：使用`let`替换`var`，使函数内部的`i`取自本次循环时的块级作用域，而不是全局作用域的`i`。

    ```js
    var arr = [];
    for (let i=0; i<3; i++){
        arr[i] = function () {
            console.log(i);
        }
    }
    arr[0]();	// 0
    arr[1]();	// 1
    arr[2]();	// 2
    ```

    

### `const`

#### `const`的使用方法

```js
const NUM = 123;
```

-   使用`const`定义的变量，无法进行修改。

    ```js
    const NUM = 123;
    NUM = 345;
    // Uncaught TypeError: Assignment to constant variable.
    ```

    **注意**：此处的变量不可修改分两种情况讨论：

    -   如果使用`const`定义的变量是原始值：无法进行修改该原始值

    -   如果使用`const`定义的变量是引用值：则无法修改该变量所指向的引用，但是具体的值可以通过引用值具有的方法来修改具体的引用值。比如：

        ```js
        const ARR = ["apple", "banana"];
        
        // 修改ARR所指的引用
        ARR = [];
        // Uncaught TypeError: Assignment to constant variable.
        ```

        ```js
        const ARR = ["apple", "banana"];
        
        // 通过Array的push方法来修改ARR的引用值
        ARR.push("orange");
        console.log(ARR);
        // ["apple", "banana", "orange"]
        ```

-   使用`const`定义变量时，声明和赋值必须写在一起。

    ```js
    const NUM;
    NUM = 123;
    // Uncaught SyntaxError: Missing initializer in const declaration
    ```

####  `const`的特性

与`let`一样，具有以下特性：

-   不进行预编译，不进行变量提升
-   同级作用域下不能重复声明
-   在块级作用域中使用`const`定义的变量，在该块级作用域外无法访问



# 解构赋值

非常有用，特别是在做数据交互的时候（如Ajax），当后台传来json数据后，可以很方便地将json数据解构为前端需要的各种变量。

## 数组解构赋值

```js
// 数组解构赋值
let [a, b, c] = [1, 2, 3];
console.log(a, b, c);
// 1 2 3
```

**练习**：已有两个变量`a` `b`，交换二者的值

```js
var a = 1,
    b = 2;
// swap a b
[a, b] = [b, a];
console.log(a, b);
// 2, 1
```

*类似于Python的`(a, b) = (b, a)`*。



## 对象解构赋值

```js
// 对象解构赋值
// 设有json对象如下：
let json = {
    "name": "tish",
    "age": 18,
    "gender": "male"
}
// 解构赋值如下：
let {name, gender:sex, age} = json;
console.log(name, age, sex);
// tish 18 male
```

-   `let {name, gender:sex, age} = json` 中只需要将键放在`{}`即可，键的顺序可以随意
-   键的名称必须要跟所赋值的对象中的键名一致，否则找不到对应的键名。如果要将所赋值对象中的键名对应到另一个名字上，则可以使用`oldKey:newKey`的形式，其中`oldKey`为所赋值对象中的键名，`newKey`为新定义的变量名，如上述代码中的`gender:sex`就是相当于`let sex = json.gender`。

当函数传入对象时，也可以在传参的位置进行解构。

```js
function show({name:user, gender:sex}) {
    console.log(user, sex);
}
show({name: "tish", gender: "male"});
// tish male
```



# 字符串

## 字符串模板

```js
var name = "tish",
    age = 18;
var str = `名字是${name}，
年龄为${age}`;
console.log(str);
// 名字是tish，
// 年龄为18
```

### 使用方法

-   使用 \`\` 来将字符串包裹
-   使用`${变量名}`来将变量赋进去

### 优点

-   \`\`支持换行，里面可以使用多行字符串

### 应用场景

-   JS生成dom结构，即已有从后端传入的json数据，批量生成dom结构

    ```html
    <ul id="list">
    </ul>
    
    <script>
        var data = [
            {title: "new1", readTimes: "100"},
            {title: "new2", readTimes: "100"},
            {title: "new3", readTimes: "100"},
            {title: "new4", readTimes: "100"},
            {title: "new5", readTimes: "100"},
        ];
        var ul = document.getElementById('list');
        for (let i=0; i<data.length; i++) {
            let {title, readTimes} = data[i];
            let li = document.createElement('li');
            li.innerHTML = `<span class="title">${title}</span>
    						<span class="readTimes">${readTimes}</span>`;
            ul.appendChild(li);
        }
    </script>
    ```

    

## 字符串的新增方法

-   字符串查找：`str.includes(subStr)`：判断`str`中是否含有`subStr`

    -   若有，返回`true`，否则为`false`

    -   相当于ES5中的`(str.indexOf(subStr) != -1)`
    -   应用实例：判断浏览器类型是否为Chrome

        ```js
        if (navigator.userAgent.includes("Chrome")) {
            console.log("Chrome");
        }else {
            console.log("not Chrome.")
        }
        // Chrome
        ```

-   字符串是否以`subStr`开头：`str.startsWith(subStr)`

    -   若是，返回`true`，否则为`false`

    -   应用实例：判断某链接地址的协议是否为https

        ```js
        var url = "https://google.com";
        console.log(url.startsWith("https"));
        // true
        ```

-   字符串是否以`subStr`结尾：`str.endsWith(subStr)`

    -   若是，返回`true`，否则为`false`

    -   应用实例：判断文件的扩展名，比如判断是否为jpg格式文件

        ```js
        var fname = "./asdf.jpg"
        console.log(fname.endsWith('jpg'));
        // true
        ```

-   重复字符串`n`次：`str.repeat(n);`

    -   `n`只能是正整数，为0时返回空串



# 扩展运算符

## `...` 用法

`…array`：将数组/类数组`array`中的元素展开。

```js
var arr = [1, 2, 3];
console.log(arr);		// [1, 2, 3]
console.log(...arr);	// 1 2 3
```



## 应用实例

-   应用实例1：给函数传参数

    ```js
    function show(...a) {
        console.log(a);
    }
    show(1,2,3,4,5);
    // [1, 2, 3, 4, 5]
    ```

    相当于：

    ```js
    function show() {
        console.log(Array.prototype.slice.call(arguments));
    }
    show(1,2,3,4,5);
    // [1, 2, 3, 4, 5]
    ```

-   应用实例2：当做rest**运算符取函数的剩余参数**

    ```js
    function show(a, b, ...restArgs) {
        console.log(a, b);
        console.log(restArgs);
    }
    show(1,2,3,4,5);
    // 1 2
    // [3, 4, 5]
    ```

-   应用实例3：浅拷贝一份数组，即原始值的引用不同，而引用值依然使用相同引用的拷贝。

    -   传统ES5写法应
        -   方法1：`new Array()`，然后循环遍历依次插入数据；
        -   方法2：使用`Array.from(array)`来浅拷贝数组`array`
        -   方法3：使用`Array.prototype.slice.call(array)`，或者`array.slice()`
    -   ES6中，使用`...`运算符可以简单地完成数组的浅拷贝。

    ```js
    var arr = [1,2,3,4,{name:'tish'}];
    var arrCopy = [...arr];
    console.log(arrCopy);
    // [1, 2, 3, 4, 5]
    ```



## 其他

-   使用`…`也可以将字符串转化为数组

    ```js
    var str = "1234567890";
    console.log([...str]);
    // ["1", "2", "3", "4", "5", "6", "7", "8", "9", "0"]
    ```

    等同于，`Array.from(str);`



# 函数

## 函数默认参数

### 默认参数用法

```js
function fn(a="1", b="2") {
    console.log(a, b);
}
fn();					// 1 2
fn("3");				// 3 2
fn(undefined, "4");		// 1 4
fn("3", "4");			// 3 4
```

### 默认参数注意事项

-   只有当传入的参数为`undefined`时，才会使用默认参数值。

-   形参赋上默认值后，函数内部不可以使用`let` `const`再次声明，否则会报错。但是可以使用`var`来重复声明。

    ```js
    function fn(a="1") {
        let a = 2;
        console.log(a);
    }
    fn();
    // Uncaught SyntaxError: Identifier 'a' has already been declared
    ```

    

## 箭头函数

### 箭头函数用法

-   `(参数) => 返回值`

    ```js
    var add = (a, b) => a + b;
    // 等同于
    var add = function (a, b) {
        return a + b;
    }
    ```
    
- `(参数) => {函数语句}`：**常用**

    ```js
    var show = (name) => {
        console.log(name);
        return true;
    }
    // 等同于
    var show = function (name) {
        console.log(name);
        return true;
    }
    ```

### 箭头函数的注意事项

- 箭头函数的`this`指向

    -   箭头函数中的`this`，**指向的是函数定义时所在的对象**，而不是谁调用`this`就指向谁。

        **实例**：等待2秒使用`json.show`打印出`json.id`
        - 如果使用正常的函数

        ```js
            var json = {
                id : 123,
                show: function () {
                    setTimeout(function () {
                        console.log(this.id);
                    }, 2000);
                }
            };
            json.show();
            // undefined
        ```

        原因是函数执行时，`console.log(this.id)`中的`this`由于是`setTimout`调用的，而`setTimeout`是window在调用，因此`this`指向的是window。

        解决该闭包问题，可以使用立即执行函数：

        ```js
            var json = {
                id: 123,
                show: function () {
                    (function (id){
                        setTimeout(function (){
                            console.log(id);
                        }, 2000)
                    }(this.id))
                }
            };
            json.show();
            // 123
        ```

        -   使用箭头函数的话，由于`this`指向函数定义时的`this`，而不是函数执行时谁调用就指向谁，因此不会产生闭包的问题。

            ```js
            var json = {
                id: 123,
                show: function () {
                    setTimeout(()=>{
                        console.log(this.id);
                    }, 2000)
                }
            };
            json.show();
            ```



- 箭头函数中没有`arguments`

    ```js
    var func = ()=>{console.log(arguments)};
    func(1,2,3);
    // Uncaught ReferenceError: arguments is not defined.
    ```

    可以使用扩展运算符来替代：

    ```js
    var func = (...args)=>{console.log(args)};
    func(1,2,3);
    // [1, 2, 3]
    ```

- 箭头函数不能当做构造函数


