<h1 style="font-size:45px;font-style:italic;font-weight:800">Vue Notes</h1>
[TOC]

# Vue简介

## 什么是Vue

Vue：一套用于构建用户界面的渐进式框架

-   **渐进式框架**：即根据不同深度的需求，使用框架中的不同的部分。

    -   比如：当只需要构建一个简单页面的时候，只需要用到Vue声明式渲染部分就可以了，如果想要进一步的模块化，则需要使用Vue的组件系统部分。

    ![](./Notes Images/progressive.png)

## 框架和库的区别

-   库是将代码及合成一个产品，共开发者去使用，开发者去调用库中的方法来实现自己的功能。比如：jQuery，zepto
    -   库相当于一个工具集、API集合
    -   使用库中方法的控制权在自己手里
-   框架是为了解决一类问题而开发出来的产品，基于自身的特点向用户提供一套完整的解决方案。比如：Vue，react
    -   使用框架时，必须按照框架的规定来写代码，比如想要写数据，就只能放到data里面

## Vue的特点

-   易用：会html css js即可
-   灵活：不断繁荣的生态系统，可以在一个库和一套完整框架之间自如伸缩
-   高效：20KB min+gzip运行大小，超快虚拟DOM，最省心的优化

## 引入Vue

### `script`标签引入

```html
<script src="https://cdn.jsdelivr.net/npm/vue@2.6.10/dist/vue.js"></script>
```



# Vue的基本语法

## 插值表达式

- 可以在标签内部执行js代码的表达式

    ```html
    <div id="app">{{ /* js代码 */] }}</div>
    <script>
    	new Vue({
            el: 'div#app'
        });
    </script>
    ```

    -   前端页面会显示`div#app`中js代码执行的结果
    -   在标签中写入的js代码，只能是三种：
        - 基本数值类型
        - 表达式
        - Vue对象的data中定义的变量
    - 其他的如语句（声明变量语句、变量赋值语句、if条件语句，for循环语句等）会报错。

    **示例**：
    
    ```html
    <div id="app">
    	<!-- 基本数值类型 -->
        {{ 'a' }}
        {{ 10 }}
        {{ true }}
        {{ [1, 2, 3] }}
        {{ {a: 1, b:10} }}
        {{ undefined }}
        {{ null }}
        
        <!-- 表达式 -->
        {{ 1+1 }}
        {{ 1-1 }}
        {{ 2*5 }}
        {{ 10/2 }}
        {{ 5%2 }}
        {{ (5>3)? 1:0 }}
        
        <!-- Vue对象data中定义的变量 -->
        {{ a }}
        {{ b }}
    </div>
    
    <script>
    	new Vue({
            el: 'div#app',
            data: {
                a: 1,
                b: 2
            }
        })
    </script>
    ```
    
    前端页面显示：
    
    <div>a 10 true [ 1, 2, 3 ] { "a": 1, "b": 10 } 2 0 10 5 1 1 2</div>

## Vue对象

### 创建Vue对象

```html
<!-- HTML部分 -->
<div id=app>{{ a }}</div>
```

```js
/* JS部分 */
let vm = new Vue({
  el: "div#app",    // vue挂载的dom对象
  data: {a: 1}		// vue对象中的数据
})
```

### 获取vue对象中的属性和方法

-   传入的`data`中的变量，可以使用`vm.[变量名]`来访问、修改
-   Vue对象总的原有属性和方法，可以使用`vm.$[变量名|方法名]`来访问、修改

```js
// 接上段代码
console.log(vm.a, vm.b);	// 1 2
cosnole.log(vm.$el);		// <div>1</div>
console.log(vm.$data);		// {__ob__: Observer}
```

**注意**：

-   修改`vm.$data`中的变量值，会实时更新（渲染）html中相应的变量值，比如：

    ```js
    // 接上段代码
    vm.a = 123;
    // 此时，网页中所有关于a变量的部分，值都变为123
    vm.$nextTick(()=>{
        console.log(vm.$el.innerHTML);	// 123
    })
    ```

    >   其中，`vm.$nextTick(callback)`为等待页面更新渲染后执行callback函数的Vue对象中的方法

- 多次修改`vm.$data`中的变量值，只会在最后一次更改之后，更新一次数据。

    ```js
    // 接上段代码
    vm.a = 234;
    vm.a = 345;
    vm.a = 567;
    vm.$nextTick(()=>{
        console.log(vm.$el.innerHTML); // 567
    })
    ```



### Vue对象对DOM元素的挂载

vue对象对DOM对象的挂载有两种方式：

- 在`new Vue()`传入的对象参数中，用`el`来定义挂载的DOM元素
- 使用`vm.$mount(‘<selector>’)$`进行DOM元素的挂载

注意：

-   `el`参数和`$mount`方法只会有一个生效，谁先定义谁就生效。
-   `el`参数和`$mount`方法必须要写一个，否则会报错。

```js
// 使用 el 参数来挂载 div.demo 元素
new Vue({
    el: 'div.demo'
});

// 使用 $mount 方法来挂载 div.demo 元素
let vm = new Vue();
vm.$mount("div.demo");
```





# Vue指令

## `v-bind` 绑定属性

-   用法：在需要绑定的属性节点前面加上`v-bind:`，那么后面的对应属性中（`“”`内）就可以使用js表达式。

    ```html
    <input type="text" v-bind:value="1+a">
    ```

    ```js
    let vm = new Vue({
        el: "input",
        data: {
            a: 123,
        }
    });
    ```

    **示例**：img标签中的属性绑定

    ```html
    <img v-bind:src="imgUrl" v-bind:alt="name">
    ```

    ```js
    let vm = new Vue({
        el: 'img',
        data: {
            name: "This is an image.",
            imgUrl: './images/pic.png'
        }
    })
    ```

-   `v-bind`的简写：`v-bind: <==> :`

    -   如果很多的属性都需要`v-bind`，那么写起来就会非常麻烦，因此推荐无论什么时候都用简写

    ```html
    <img :src="imgUrl" :alt="name">
    ```

    ```js
    let vm = new Vue({
        el: "img",
        data: {
            name: "This is an image.",
            imgUrl: "./images/pic.png"
        }
    })
    ```

### 两种特殊属性的`v-bind`

有两种特殊属性的`v-bind`与其他属性不一样，`class`和`style`

- `class`：因为`class`属性中常含有多个类名，因此跟其他属性有一点不一样。使用`v-bind`绑定`class`属性的方法为：

    -   用**数组**的形式将**多个Vue变量名**写在属性中，其中每个变量对应一个类

        ```css
        /* CSS */
        .red {
            background-color: #f00;
        }
        .box {
            display: block;
            width: 100px;
            height: 100px;
        }
        ```

        ```html
        <!-- HTML -->
        <div :class="[c1, c2]"></div>
        ```

        ```js
        /* JS */
        let vm = new Vue({
            el: "div",
            data: {
                c1: "red",
                c2: "box"
            }
        })
        ```
    
    -   用**对象**的形式将**多个类**写在属性中，对象的每个key为一个类名，value为`true`|`false`来决定是否要加入该类名
    
        ```html
        <div :class="{red:true, box:true}"></div>
        ```
    
    -   用对象数组的形式将类写在属性中，相当于上两种方式的结合
    
        ```html
        <div :class="[{red: true}, {box: true}]"></div>
        ```

-   `style`：与`class`类似

    -   使用对象来添加行间样式

        ```html
        <div :style="{width: '100px', height: '100px', background-color: 'red'}"></div>
        ```

    -   使用对象数组来添加多组样式

        ```html
        <div :style="[{width: '100px', height: '100px'}, {background-color: 'red'}]"></div>
        ```

        

## `v-on`监听事件

-   用法：在标签中添加`v-on:[事件名]="handle(args)”`，并将`handle`函数放进Vue对象中的`methods`中。

    ```html
    <div id="app" v-on:click="handleClick"></div>
    ```

    ```js
    let vm = new Vue({
        el: "#app",
        methods: {
            handleClick () {
                console.log("You clicked the #app.")
            }
        }
    })
    ```

-   `v-on:`可以简写为`@`，因此上面的html代码也可以写为：

    ```html
    <div id="app" @click="handleClick"></div>
    ```

    

## 条件渲染

### `v-if`

-   `v-if`能够控制元素的显示与隐藏

    ```html
    <div v-if="show" style="width:100px; height:100px; border:1px solid black;"></div>
    ```

    ```js
    const vm = new Vue({
        el: "div",
        data: {
            show: true
        },
    })
    ```



###  `v-else-if` `v-else`

-   `v-else-if` `v-else`需要配合`v-if`来使用，并且之间不能有其他不含该指令的标签

    ```html
    <div id="app">
    	<div v-if="num === 0" class="box red"></div>
        <div v-else-if="num === 1" class="box blue"></div>
        <div v-else class="box green"></div>
    </div>
    ```

    ```js
    const vm = new Vue({
        el: "#app",
        data: {
            num: 0
        }
    })
    ```

    通过改变`vm.num`的值，可以实现不同的div可以被显示。

-   当需要控制一组元素显示与隐藏的时候，每个元素都添加`v-if`太麻烦，因此可以通过使用虚拟dom元素`template`标签来包裹所需要添加`v-if`

    ```html
    <template v-if-"show">
        <div class="box red"></div>
        <div class="box blue"></div>
        <div class="box green"></div>
    </template>
    ```

    ```js
    const vm = new Vue({
        el: "#app",
        data: {
            show: true
        }
    })
    ```



### `v-show`

- `v-show`：也能够控制元素的显示与隐藏
  
    ```html
    <div v-show="show" class="box red"></div>
    ```
	
	```js
	const vm = new Vue({
	    el: 'div',
	    data: {
	        show: true
	    }
	})
	```

- `v-show`与`v-if`的区别在于：
  
    -   `v-if`是通过**删除和添加dom元素**来实现元素的显示与隐藏，`v-show`是通过**添加行间样式**`display:none`来实现元素的显示与隐藏
    -   由于频繁操作dom会造成性能浪费，因此:
	        -   当需要某元素频繁地显示与隐藏时，最好是通过`v-show`来实现
	        -   当只需要判断一次某元素是否要显示与隐藏，以后都不改变的时候，比如一开始打开页面决定某些元素是否要显示时，要使用`v-if`
	-   `template`虚拟dom元素上不能添加`v-show`
	    -   原因：虚拟dom元素最终并不真的添加在页面当中，而`v-show`是通过控制行间样式中的`display: none`来控制显示与隐藏，因此控制`template`的`display`属性并不对其包裹的dom元素产生影响
