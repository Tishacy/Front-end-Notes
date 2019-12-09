<h1 style="font-size:45px; font-style: italic; text-align: center;">Vue Notes</h1>

[TOC]

-   实例化Vue对象：

    ```html
    <!-- HTML -->
    <div id="vue-app">
        <h1>{{ name }}</h1>
        <h1>{{ greet() }}</h1>
    </div>
    ```

    ```js
    /* JS */
    new Vue({
        el: "#vue-app",
        data: {
            name: "Tishacy"
        },
        methods: {
            greet: function () {
                return "Hello Vue!";
            }
        }
    });
    ```

    -   `el`：[string, Dom object] element 需要获取的元素，一定是html中的根容器元素
    -   `data`：[json] 用于数据的存储
        -   如果数据时原始值，可以直接使用插值表达式`{{ 变量名 }}`在html中的vue容器内引用
        -   `data`中的变量，在vue对象中可以使用`this.变量名`来获取变量值
    -   `methods`：[json] 用于方法的存储
        -   可以直接使用插值表达式`{{ 函数() }}`在html中的vue容器内执行函数

-   属性绑定：

    -   `v-bind`：简写为（`:`），后面可以加vue.data中的变量

    -   示例：

        ```html
        <div id="vue-app">
            <img v-bind:src="imgUrl">
        </div>
        ```

        ```js
        const vue = new Vue({
            el: '#vue-app',
            data: {
                imgUrl: <image url>,
            }
        })
        ```

-   事件监听：

    -   `v-on`：简写为（`@`），可以用来监听事件

    -   示例：

        ```html
        <div id="vue-app">
            <button v-on:click="handleClick"></button>
        </div>
        ```

        ```js
        const vue = new Vue({
            el: '#vue-app',
            methods: {
                handleClick() {
                    console.log("You clicked the button.");
                }
            }
        })
        ```

-   事件修饰符：

    -   `.stop`：阻止事件冒泡

    -   `.prevent`：阻止默认事件

    -   `.capture`：使用事件捕获

    -   `.once`：只执行一次事件

    -   示例：

        ```html
        <div id="vue-app">
            <!-- 点击一次事件 -->
            <div @click.once="clickOnce"></div>
            
            <!-- 阻止a标签跳转事件 -->
            <a @click.prevent href="https://baidu.com">To Baidu</a>
        </div>
        ```

-   键盘事件