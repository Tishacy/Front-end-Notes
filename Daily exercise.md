# 前端知识每日练习

1. 下面的代码执行时输出什么？说明。

   ```js
   console.log(false == '0');
   console.log(false === '0');
   ```

   执行结果为：

   ```js
   true
   false
   ```

   知识点：`==`的隐式类型转换和`===`用法

   解析：

   在JavaScript中，有两套相等运算符。三重相等运算符`===`的行为与任何传统的相等运算符相同：如果两侧的表达式具有相同的类型和相同的值，则计算结果为`true`。然而，双等号运算符在比较它们之前试图进行类型转换成`Number()`，将`false`转换为`0`,`‘0’`也转换为`0`，二者相等。因此，通常使用`===`来判断二者是否真的相等，而不是`==`。对于`!==`和`!=`也是如此。

   

2. 考虑下面的代码。输出是什么，为什么？

   ```js
   (function () {
       try {
           throw new Error();
       }catch(x) {
           var x = 1, y = 2;
           console.log(x);
       }
       console.log(x);
       console.log(y);
   })();
   ```

   执行结果为：

   ```js
   1
   undefined
   2
   ```

   知识点：预编译、作用域链、`with`和`catch`对作用域链的改变

   解析：

   1. 函数预编译：在函数预编译时，`var`语句被挂起（不包含值的初始化）到它所属的全局或函数作用域的顶部。即：

      ![](./Notes Images/exercise 2-1.png)

   2. **`with`与`catch`内的在执行时，其执行期上下文的作用域会被临时改变**，生成了一个新的可变对象并推入作用域链的头部，此时函数原本的局部变量现在处于第二个作用域链对象中。见下图：

      ![](./Notes Images/exercise 2-2.png)

      此时，第六行的`console.log(x)`从临时对象`catch variable object`中索取`x`，输出1。

   3. `catch`语句执行结束后，catch临时生成的可变对象被销毁，此时作用域链的顶端为函数的AO，如下图：

      ![](./Notes Images/exercise 2-3.png)

      此时，第8行和第9行分别输出`x`和`y`的值，得到`undefined`和`2`。

   

3. 以下代码的输出是什么？解释你的答案。

   ```js
   var a = {},
       b = {key: 'b'},
       c = {key: 'c'};
   a[b] = 123;
   a[c] = 456;
   console.log(a[b]);
   ```

   知识点：对象的属性访问与赋值

   解析：

   访问对象的属性有两种方式，一种是`obj.prop`，另一种是`obj[str(prop)]`。

   - 其中`obj.prop`会隐式的将`prop`使用`toString()`方法转化成为字符串类型`str(prop)`，然后再调用`obj[str(prop)]`。
   - 使用`obj[prop]`时，如果`prop`不是字符串，那么也会隐式调用`toString()`方法转化为字符串类型的`str(prop)`，再调用`obj[str(prop)]`。

   因此，第4行的`a[b]`相当于`a[b.toString()]`，由于`b`是对象，则`b.toString()`为`"[object Object]"`，即相当于`a["[object Object]"]=123`，此时`a`为：

   ```js
   a = {
       "[object Object]": 123,
   }
   ```

   同理，第5行的`a[c]-->a[c.toString()]-->a["[object Object]"]`，并赋值为``456`，将原`"[object Object]"`属性值覆盖成了`456`。

   ```js
   a = {
       "[object Object]": 456,
   }
   ```

   因此，第6行`console.log(a[b])`相当于`console.log(a["object Object"])`得到`456`。

   

4. 考虑下面两个函数，他们都会返回同样的值吗？为什么或者为什么不？

   ```js
   function foo1() {
       return {
           bar: "hello"
       };
   }
   
   function foo2() {
       return
       {
           bar: "hello"
       };
   }
   ```

   解析：不会返回同样的值，`foo1();`执行返回为对象`{bar: 'hello'}`，而`foo2();`执行返回空值，为`undefined`。

   因为如果每一行代码是一个可执行的语句，虽然没有加`;`，但是系统会隐式的添加上`;`，然后解释一行执行一行，第二个函数执行到`return`就停止了。

   因此，JavaScript通常约定一行开头的大括号应放在行尾，而不是放在新行的开头，避免出现上述情况。

   

5. 以下代码的输出是什么，解释你的答案。如何在这里使用闭包？

   ```js
   for (var i=0; i<5; i++) {
       setTimeout(function() {
           console.log(j);
       }, j * 1000);
   }
   ```

   知识点：

   - `setTimeout(func, delay)`：延时`delay`毫秒后执行`func`
   - 闭包

   解析：

   - 代码输出为：每隔1000毫秒，就打印一个5

   - 使用立即执行函数来解决闭包问题，实现每隔1000毫秒，就依次打印0,1,2,3,4。

     ```js
     for (var i=0; i<5; i++) {
         (function (j){
             setTimeout(function() {
                 console.log(j);
             }, j * 1000);
         }(i));
     }
     ```

   - 在ES2015上下文中，可以在原始代码中简单地使用`let`而不是`var`

     ```js
     for (let i=0; i<5; i++) {
         setTimeout(function(){
             console.log(i);
         }, j*1000);
     }
     ```

6. 代码返回后会怎么样？

   ```js
   console.log(typeof typeof 1);
   ```

   知识点：`typeof`函数返回的结果是字符串形式的。

   解析：首先执行后面的`typeof 1`得到`"number"`，然后执行`typeof "number"`得到`"string"`。

7. 代码会返回什么？

   ```js
   typeof undefined == typeof NULL
   ```

   知识点：`typeof`用法

   解析：结果返回`true`。`NULL`是未定义的变量（注意不是`null`），未定义的变量使用`typeof`不会报错，而是返回`"undefined"`。`typeof undefined`也会返回`"undefined"`，因此会返回`true`。

8. 以下代码输出什么？为什么？

   ```js
   var b = 1;
   function outer() {
       var b = 2;
       function inner() {
           b++;
           var b = 3;
           console.log(b);
       }
       inner();
   }
   outer();
   ```

   执行结果：

   ```js
   3
   ```

   知识点：函数预编译及作用域链

   解析：

   1. 全局预编译，作用域链如下：

      ```js
      [[scope]] = {
          GO: {
              b: undefined,
              outer: function outer () {/*...*/}
      	}
      }
      ```

   2. 执行第1行，将`GO`中的`b`赋值为1，此时作用域链为：

      ```js
      [[scope]] = {
          GO: {
              b: 1,
              outer: function outer () {/*...*/}
      	}
      }
      ```

   3. 执行第11行，执行`outer`函数进行该函数的预编译，此时作用域链为：

      ```js
      [[scope]] = {
          outer_AO: {
              b: undefined;
              inner: function inner() {/*...*/}
          }
          GO: {
              b: 1,
              outer: function outer () {/*...*/}
      	}
      }
      ```

   4. 执行函数`outer`中的`b=2`，将`outer_AO`中的`b`赋值为2，此时作用域链为：

      ```js
      [[scope]] = {
          outer_AO: {
              b: 2;
              inner: function inner() {/*...*/}
          }
          GO: {
              b: 1,
              outer: function outer () {/*...*/}
      	}
      }
      ```

   5. 执行函数`outer`中的`inner()`，执行前进行`inner`函数的预编译，此时作用域链为：

      ```js
      [[scope]] = {
          inner_AO: {
              b: undefined
          }
          outer_AO: {
              b: 2;
              inner: function inner() {/*...*/}
          }
          GO: {
              b: 1,
              outer: function outer () {/*...*/}
      	}
      }
      ```

   6. 执行函数`inner`的`b++`，就是将`inner_AO`中的`b = b + 1 = undefined + 1 = NaN`。此时作用域链为：

      ```js
      [[scope]] = {
          inner_AO: {
              b: NaN
          }
          outer_AO: {
              b: 2;
              inner: function inner() {/*...*/}
          }
          GO: {
              b: 1,
              outer: function outer () {/*...*/}
      	}
      }
      ```

   7. 执行函数`inner`的`b=3`，将`inner_AO`中的`b`赋值为3，此时作用域链为：

      ```js
      [[scope]] = {
          inner_AO: {
              b: 3
          }
          outer_AO: {
              b: 2;
              inner: function inner() {/*...*/}
          }
          GO: {
              b: 1,
              outer: function outer () {/*...*/}
      	}
      }
      ```

   8. 执行函数`inner`中的`console.log(b)`，从作用域链顶端依次往下找，直到找到`b`变量，因此从`inner_AO`中找到`b=3`，输出3。

   

