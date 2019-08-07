[TOC]

# jQuery简介

-   jQuery是一个JavaScript库
-   使用jQuery能够使HTML文档遍历和操作、事件处理、动画以及ajax更加方便
-   jQuery版本：
    -   1.x：（优先选择）兼容IE678，相对其他版本文件较大，官方只做bug维护，功能不再新增
    -   2.x：不兼容IE678，相对1.x文件较小，官方只做bug维护，功能不再新增
    -   3.x：不兼容IE678，只支持最新的浏览器，很多老的jQuery插件不支持这个版本，相对1.x文件较小，提供不包含ajax/动画api版本。



# jQuery的使用

## 下载使用

1.  下载jQuery：[jQuery下载地址](http://code.jquery.com/)

2.  引入下载的jQuery库

    ```html
    <script src="js/jquery-1.12.4.js"></script>
    ```

3.  编写jQuery代码

    ```html
    <script>
    	$(document).ready(function () {});
    </script>
    ```

## 非下载使用

1.  引入在线jQuery库

    ```html
    <script src="http://code.jquery.com/jquery-1.12.4.min.js"></script>
    ```

2.  编写jQuery代码



# jQuery基础

## jQuery的冲突问题

在引入jQuery库之后，如果还引入其他也使用`$`的库，那么就会造成冲突问题。可以通过以下方法来解决冲突问题

1. 释放`$`的使用权
    ```js
    jQuery.noConflict();
    ```

    -   释放操作必须在编写其他jQuery代码之前
    -   释放之后就不能再使用`$`，而改为使用`jQuery`

2.  自定义一个访问符号

    ```js
    var sig = jQuery.noConflict();
    sig(function() {
        alert("hello sig");
    })
    ```

    