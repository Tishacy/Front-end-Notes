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



# 数组

## ES5新增的方法

-   `arr.forEach(callback, this=window)`：代替普通的for循环，无返回值

    -   回调函数`callback`中的形参分别为`val` `index` `arr`
    -   `this`参数可以修改循环中的`this`指向
        -   如果回调函数是箭头函数的话，那么this指向不改变，即始终指向函数定义时的对象。

    ```js
    let arr = [1, 2, 3, 4, 5];
    
    arr.forEach(function (val, index, arr) {
        console.log(this, val, index, arr);
    }, 123);
    // Number(123) 1 0 [1, 2, 3, 4, 5]
    // Number(123) 2 1 [1, 2, 3, 4, 5]
    // Number(123) 3 2 [1, 2, 3, 4, 5]
    // Number(123) 4 3 [1, 2, 3, 4, 5]
    // Number(123) 5 4 [1, 2, 3, 4, 5]
    
    arr.forEach((val, index, arr)=>{
        console.log(this, val, index, arr);
    }, 123);
    // Window 1 0 [1, 2, 3, 4, 5]
    // Window 2 1 [1, 2, 3, 4, 5]
    // Window 3 2 [1, 2, 3, 4, 5]
    // Window 4 3 [1, 2, 3, 4, 5]
    // Window 5 4 [1, 2, 3, 4, 5]
    ```

-   `arr.map(callback, this)`：**非常有用**，映射

    -   配合return，返回一个新数组
    -   没有return时，相当于`forEach`，会返回含有`arr.length`长度的数组，但是内部元素全是`undefined`。

    ```js
    let arr = [1, 2, 3, 4, 5];
    
    let newArr = arr.map((val, index, arr)=>{
    	val += 10;
    });
    console.log(newArr);
    // [undefined, undefined, undefined, undefined, undefined]
    
    let newArr1 = arr.map((val, index, arr)=>{
        val +=10;
        return val;
    });
    console.log(newArr1);
    // [11, 12, 13, 14, 15]
    ```

    -   应用实例：更改数组的数据结构

        ```js
        var data = [
            {'title': 'aaa', read: 100},
            {'title': 'bbb', read: 200},
            {'title': 'ccc', read: 300}
        ];
        
        var newData = data.map((item, index, arr)=>{
            return {
                't': item.title,
                'r': item.read + 200,
            }
        })
        console.log(newData);
        // [{…}, {…}, {…}]
        // 0: {t: "aaa", r: 300}
        // 1: {t: "bbb", r: 400}
        // 2: {t: "ccc", r: 500}
        // length: 3
        // __proto__: Array(0)
        ```

-   `arr.filter(callback)`：根据条件过滤元素

    -   回调函数`callback`返回值为`true`就保留该元素，`false`就去除该元素

    ```js
    var data = [
        {'title': 'aaa', read: 100},
        {'title': 'bbb', read: 200},
        {'title': 'ccc', read: 300}
    ];

    // 筛选出read＞100的数据
    var filteredData = data.filter((item, index, arr)=>{
        return item.read > 100;
    })
    console.log(filteredData);
    // (2) [{…}, {…}]
    // 0: {title: "bbb", read: 200}
    // 1: {title: "ccc", read: 300}
    // length: 2
    // __proto__: Array(0)
    ```

-   `arr.some(callback)`：数组里面存在一个元素符合条件，返回`true`

-   `arr.every(callback)`：数组里面所有元素都符合条件，返回`true`

    ```js
    var data = [
        {'title': 'aaa', read: 100},
        {'title': 'bbb', read: 200},
        {'title': 'ccc', read: 300}
    ];
    
    // 判断data中是否有title为aaa的元素
    var some = data.some((item, index, arr)=>{
        return item.title == 'aaa';
    });
    console.log(some);	// true
    
    // 判断data中是否所有title都是aaa
    var every = data.every((item, index, arr)=>{
        return item.title == 'aaa';
    });
    console.log(every);	// false
    ```

-   `arr.reduce(callback)`：

    -   回调函数`callback`有四个参数，分别是`prev` `cur` `index ` `arr`
    -   其中`prev`是上一次`callback`的返回结果，第一次循环的时候为数组的第一个值
    -   应用实例：求数组的和、连乘

    ```js
    var arr = [1,2,3,4,5,6,7,8,9,10];
    
    // 数组求和
    console.log(arr.reduce((prev, cur, index, arr)=>{
        return prev + cur;
    }));
    // 55
    
    // 数组的连乘
    console.log(arr.reduce((prev, cur, index, arr)=>{
        return prev * cur;
    }))
    // 3628800
    ```

-   `arr.reduceRight(callback)`：

    -   与`reduce`用法相同，只不过是从右往左遍历数组并赋值给`cur`（最后一个元素除外）进行运算。
    -   `prev`是上一次`callback`的返回结果，第一次循环的时候为数组的最后一个值



## ES6新增的方法

-   `for <Item> of <Array Iterator> `

    -   `arr.values()`：数组值Iterator

    -   `arr.keys()`：数组下标Iterator

    -   `arr.entries()`：数组实体Iterator

    -   `for <Item> of <Array Iterator>`用法：

        ```js
        var arr = [1,2,3,4,5,6,7,8,9,10];
        
        for (let val of arr.values()) {	// 或者直接 for (let val of arr) {...}
            console.log(val);
        }
        
        for (let key of arr.keys()) {
            console.log(key);
        }
        
        for (let [key, val] of arr.entries()) {
            console.log(key, val);
        }
        ```

-   `arr.find(callback)`：找出第一个符合条件的数组元素，如果没找到，返回`undefined`

-   `arr.findIndex(callback)`：找出第一个符合条件的数组元素的索引，如果没找到，返回`-1`

-   `arr.includes(item)`：判断数组中是否含有某元素，有则返回`true`，反则返回`false`



# 对象

## 对象简洁语法

```js
var name = "aaa",
    age = 123;

let obj = {
    name,	// 相当于 name: name
    age,	// 相当于 age: age
    eat() {	// 相当于 eat: function () {...}, 注意不能是箭头函数
        console.log('eating');
    }
}
```

实例：新建Vue实例

```js
new Vue({
    router,
    App,
    vuex
});
```



## 对象新增方法

-   `Object.is()`：比较两个值是否相等，长得一样的都返回`true`

    -   `Object.is(NaN, NaN)`：`true`
    -   `Object.is(+0, -0)`：`false`

-   `Object.assign(target, source1, source2, ...)`：

    -   用法1：合并对象

        -   `target`通常使用一个新的空对象`{}`，`source1, source2, …` 是需要合并的对象，如果有重复的`key`，则后者覆盖前者。

        -   常见用法如下：

            ```js
            let json = {a:1},
                json2 = {b:2, a:2},
                json3 = {c:3};
            let data = Object.assign({}, json, json2, json3);
            console.log(data);
            // {a:2, b:2, c:3}
            ```

            再比如，使用Ajax时，合并默认参数和用户传入的参数：

            ```js
            function ajax(options) {
                let defaults = {
                    type: 'get',
                    header: '',
                    data: {}
                }
                let totalOptions = Object.assign({}, defaults, options);
            }
            ```

    -   用法2：浅拷贝一个对象或数组

      -   `let copiedArr = Object.assign([], arr)`
      
      -   `let copiedObj = Object.assing({}, obj)`
      
      -   注意：该方法是浅拷贝
      
          ```js
          let arr = [1,2,3,{a:123}, [3,4,5]];
          let newArr = Object.assign([], arr);
          console.log(arr);
          console.log(newArr);
          newArr[0] = 123;
          newArr[3].a = 456;
          newArr[4][1] = 987;
          console.log(arr);
          console.log(newArr);
          // [ 1, 2, 3, { a: 123 }, [ 3, 4, 5 ] ]
          // [ 1, 2, 3, { a: 123 }, [ 3, 4, 5 ] ]
          // [ 1, 2, 3, { a: 456 }, [ 3, 987, 5 ] ]
          // [ 123, 2, 3, { a: 456 }, [ 3, 987, 5 ] ]
          ```



# Promise

## 语法

```js
const promise = new Promise(function (resolve, reject){
    // resolve	成功时调用
    // reject	失败时调用
    if (/* 条件 */) {
        resolve("Success!");
    } else {
        reject("Failed!");
    }
});
promise.then(res=>{
    console.log(res);
}, err=>{
    console.log(err);
})
```

常用方法：

- 一般用法：  
    ```js
    new Promise().then(res=>{/* ... */}).catch(err=>{/* ... */});
    ```
    
- `Promise.resolve('aa’)`：将现有的东西，转成一个promise对象，并且是resolve的状态

    - 等价于：

        ```js
        new Promise(resolve => {
            resolve('aa');
        });
        ```

-   `Promise.reject(‘aaa’)`：将现有的东西，转成一个promise对象，并且是reject的状态

    -   等价于：

        ```js
        new Promise((resolve, reject) => {
            reject('aaa');
        });
        ```

-   `Promise.all([p1, p2, p3])`：将promise对象放一个数组中，转成一个promise对象，数组中的promise对象全都是resolve状态（成功）时才执行`then`。

    -   `Promse.all`在处理多个异步处理时非常有用，比如说一个页面上需要等两个或多个ajax的数据回来以后才正常显示，在此之前只显示loading图标。

    -   `Promise.all`获得的成功结果的数组里面的数据顺序，和`Promise.all`接收到的数组顺序是一致的，即`p1`的结果在前，即便`p1`的结果获取的比`p2`要晚。这带来了一个绝大的好处：在前端开发请求数据的过程中，偶尔会遇到发送多个请求并根据请求顺序获取和使用数据的场景，使用`Promise.all`毫无疑问可以解决这个问题。

    -   例子：

        ```js
        let p1 = Promise.resolve('aaa');
        let p2 = Promise.resolve('bbb');
        let p3 = Promise.resolve('ccc');
        
        Promise.all([p1, p2, p3]).then(res => {
            // p1 p2 p3全都异步执行得到结果后所执行的函数
        	let [res1, res2, res3] = res;
            console.log(res1, res2, res3);
            // 'aaa' 'bbb' 'ccc'
        })
        ```

-   `Promise.race([p1, p2, p3])`：将promise对象放一个数组中，转成一个promise对象，数组中的promise对象哪一个结果返回得快，就返回那个结果，不管结果本身是成功状态还是失败状态。

    -   例子：

        ```js
        let p1 = new Promise((resolve, reject) => {
            setTimeout(() => {
                resolve("SUCCESS");
            }, 1000);
        })
        let p2 = new Promise((resolve, reject) => {
            setTimeout(() => {
                reject("FAILED");
            }, 500);
        })
        
        Promise.race([p1, p2]).then((res) => {
            console.log(res);
        }).catch((err) => {
            console.log(err);	// 'FAILED'
        })
        ```

        



# 模块化

注意：需要放到服务器环境。

## 模块化历史

-   在ES6之前，社区制订一套模块规范：
    -   Commenjs：主要是服务器端，比如Node.js通过`require(‘http’)`来引入模块
    -   AMD：主要是客户端，比如requireJs, curlJs
    -   CMD：主要是客户端，比如seaJs
-   ES6出来后，统一了服务端和客户端模块规范

## 基本用法

-   如何定义模块

    ```js
    // ./module.js
    export const a = 123;
    
    const x = 1,
          y = 2,
          z = 3;
    export {x, y, z};
    
    const d = 5;
    export default d;
    ```

-  如何使用模块

    ```html
    <script type="module">
        import './module.js';	             // 相当于直接引入（执行）模块文件
        import {a} from './module.js';	     // 需要在模块文件中 export {a};
        import {x as xx, y  as yy, z as zz} from './module.js';  // 需要在模块文件中 export {x, y, z};
        import d from './module.js';         // 需要在模块文件中 export default d;
        import * as mod from './module.js';  // 需要在模块文件中 export 需要挂在 mod对象中的变量和函数
        
        console.log(a);				// 123
        console.log(xx, yy, zz);	// 1 2 3
        console.log(d);				// 5
        console.log(mod.a, mod.x) 	// 123 1
    </script>
    ```

-   特点：
    -   `import`可以是相对路径，也可以是绝对路径
    -   `import`相同的模块只会导入一次
    -   `import`有提升功能，自动提升到顶部首先执行
    -   导入的模块内容如果有定时器更改变量内容，那使用该模块时也会发生改动。



## 动态引入

`import()`类似node里面的`require`，可以动态引入。使用方法如下：   

```js
import('./module.js').then(res=>{
    console.log(res.a + res.b);
});
```

-   优点：
    -   按需加载
    -   可以写到`if`中
    -   模块路径也可以是动态的路径，如路径可以是一个函数的执行结果



# 类和继承

## 类的定义

### 声明形式

```js
class Person {
    constructor(name, age) {	// 调用new时自动执行
        this.name = name;		// 实例的属性
        this.age = age;
    }
    showName() {				// 实例的方法
        return `名字为 ${this.name}`;
    }
    showAge() {
        return `年龄为 ${this.age}`;
    }
    static sayHi(){				// 静态方法，类的方法
        console.log('Hello ES6.');
    }
}
```

相当于ES5中对类的定义：

```js
function Person(name, age) {
    this.name = name;
    this.age = age;
}
Person.prototype.showName = function() {	// 实例的方法
    return `名字为 ${this.name}`;
}
Person.prototype.showAge = function() {		// 实例的方法
    return `年龄为 ${this.age}`;
}
Person.sayHi = function() {					// 静态方法，类的方法
    console.log('Hello ES6.');
}
```

```js
let pers = new Person('tish', 18);
console.log(pers.showName());	// tish
Person.sayHi();					// Hello ES6.
```



### 表达式形式

```js
let Person = class {
    constructor(name, age) {  
        this.name = name;
        this.age = age;
    }
    showName() {console.log(this.name)};
}
```

### 注意

-   使用`class`定义的类，本质上是构造函数

    ```js
    let Person = class{};
    console.log(typeof Person);
    // function
    ```

-   使用`class`定义的类，必须配合`new`使用，否则会报错

    ```js
    let Person = class{};
    Person();
    // Uncaught TypeError: Class constructor Person cannot be invoked without 'new'
    ```

-   使用`class`定义没有提升功能，必须在定义类之后再调用。



## 类的继承

```js
// 父类
class Person{
    constructor(name){
        this.name = name;
    }
    showName() {
        console.log(`名字是 ${this.name}`);
    }
}

// 子类
class Student extends Person {
    constructor(name, skill) {
        super(name);		// 这一句相当于是执行父类的构造函数，把父类的属性和方法拿过来，而且是继承所必需有的，否则会报错
        this.skill = skill;
    }
    showName() {
        super.showName();	// 调用父级的方法执行
    }
    showSkill() {
        console.log(`Skill is ${this.skill}`);
    }
}

// 调用
let stu = new Student('tish', 'study');
stu.showName();		// 名字是 tish
stu.showSkill();	// Skill is study
```

-   使用`class Son extends Parent`来实现继承
-   其中子类的构造函数`constructor`中必须使用`super()`来将父级的属性和方法继承过来
-   在子类中调用父类的方法时，使用`super.parentMethod()`来实现





# Generator

-   generator函数：为了解决异步问题

-   语法：

    ```js
    function * gen() {
        yield "Hello";
        yield "This is a generator";
        yield "Done!";
        return "return的东西打印不出来"
    }
    let gt = gen();
    // generator函数返回的是一个generator对象
    
    // 依次获取返回值
    console.log(gt.next());
    // {"value": "Hello", "done": false}
    
    // 遍历生成器
    for (let val of gt) {
        console.log(val);
    }
    // Hello
    // This is a generator
    // Done!
    
    // 解构赋值
    let [a, b, c] = gen();
    let [d, ...e] = gen();
    let genArr = [...gen()];
    console.log(a, b, c);
    // Hello This is a generator Done!
    console.log(d, e);
    // Hello ["This is a generator", "Done!"]
    console.log(genArr);
    // ["Hello", "This is a generaotr", "Done!"]
    ```



# `Async/await`

-   语法：

    -   在异步函数外加上关键字`async`，并在函数内部配合关键字`await`来使用
        
        -   `async`函数内部凡是对`promise`对象的操作均需要配合`await`来得到该`promise`对象所`resolve`的结果
    -   示例：
        ```js
        async function getData(url) {
            try {
                const response = await fetch(url);
                const jsonData = await response.json();
                return jsonData;	      // 相当于promsie对象中的 resolve(jsonData)        
            } catch(err) {
                throw new Error("出错了"); // 相当于promise对象中的 reject("出错了")   
            }
        }
        getData('<api url>').then(data => console.log(data),
                                  err => console.log(err));
        ```
-  特点：
    -  `await`只能放到`async`函数中
    -  `await`后面可以是`promise`对象，也可以是数字、字符串、布尔值等
    -  只要`await`语句后面`promise`状态变成reject，那么整个`async`函数就会中断执行
    -  `async`函数返回的是一个`promise`对象
    -  由于大部分`await`后面的东西都不能保证成功resolve，因此建议凡是需要使用`await`的地方都用`try…catch...`包装起来

-  

