<h1 style="fontsize:28px;text-align:center;font-family:Georgia;font-style:italic;color:#333">ES6</h1>
[TOC]

# 声明变量

-   块级作用域:
    -   凡是遇到`{}`的时候，`{}`内部的作用域称之为块级作用域
    -   比如：`if (){/*xxx*/}` `for (){/*xxx*/}` `while () {/*xxx*/}` `function () {/*xxx*/}`
    -   块级作用域存在父子结构时，子作用域可以访问父级作用域的变量。



## 变量声明的方法

### `let`

#### `let`的使用方法

`let`的使用方法与`var`一致：

```js
let variable = value;
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

    

    