<h1 style="font-size:40px; font-style:italic">Canvas Notes</h1>
[TOC]

# 1. canvas简介

## 1.1 什么是canvas

-   `canvas`是HTML5提供的一种新标签

	```html
	<canvas></canvas>
	```

-   `canvas`是一个矩形区域的**画布**，可以用JavaScript在上面绘画，控制器每一个像素

-   `canvas`标签使用JavaScript在网页上绘制图像，本身不具备绘画功能

-   `canvas`拥有多种绘制路径、矩形、圆形、字符以及添加图像的方法

-   HTML5之前的web页面只能用一些固定样式的标签：比如`p` `div` `h1`等，但有了`canvas`，web页面可以丰富起来

## 1.2 canvas主要应用的领域

- 游戏：canvas在基于web的图像显示方面比flash更加立体、更加精巧，canvas游戏在流畅度和跨平台方面更牛。
- 可视化数据，数据图表化，比如：百度的echarts
- banner广告：HTML5在banner上，用canvas实现动态的广告效果非常合适
- 未来=>模拟器：无论从视觉效果还是核心功能方面，模拟器产品可以完全由JavaScript来实现
- 未来=>远程计算机控制：canvas可以让开发者更好的基于web的数据传输，构建一个完美的可视化控制界面
- 未来=>图形编辑器：Photoshop图形编辑器基于web实现
- 其他可嵌入网站的内容：类似图表、音频、视频，还有很多元素能够更好的和web融合，并且不需要插件
- 完整的canvas移动化应用



# 2. canvas绘图基础

## 2.1 canvas标签

### 2.1.1 canvas标签语法和属性

-   canvas：普通html双标签
-   可以给标签设置`width` `height`属性，属性单位必须是px，否则忽略

注意：

-   不要使用css设置canvas的宽高，否则会进行拉伸变形

-   重新设置canvas的宽高属性会让画布擦除所有的内容
-   可以给canvas画布设置背景色

### 2.1.2 浏览器不兼容处理

-   IE9及以上才支持canvas，其他Chrome、firefox、safari等浏览器都支持

-   只要浏览器兼容canvas，那么就会支持绝大部分api（个别最新的api除外）

-   移动端的兼容情况非常理想，基本上随便使用

-   2d的支持都非常好，3d的只有IE11及以上才支持，其他浏览器都支持

-   如果浏览器不兼容，最好进行友好提示，直接写在`canvas`标签里，

    -   当浏览器兼容时，会将`canvas`标签作为画布进行处理
    -   如果不兼容时，则会当做普通`div`处理，因此写进去的友好提示会显示出来

    ```html
    <canvas>
    	您的浏览器不支持canvas，请升级浏览器。
    </canvas>
    ```

-   当浏览器不兼容时，可以在`canvas`标签中使用flash等手段进行降级

    ```html
    <canvas>
    	<!--flash-->
    </canvas>
    ```



## 2.2 canvas绘图上下文 context

### 2.2.1 context

-   context：canvas的上下文、绘制环境，是所有的绘制操作api的入口或者集合
-   `Context`对象就是JavaScript操作`canvas`的接口
-   使用`[CanvasElement].getContext("2d")` 来获取2D绘图上下文

```js
var canvas = document.getElementById("canvasId");
var ctx = canvas.getContext("2d");
// 3D的使用"webgl"
// var 3dCtx = canvas.getContext("webgl");
```

### 2.2.2 基本函数

-   画笔移动到某处：`ctx.moveTo(x, y)`：
-   画笔描边：`ctx.stroke()`
-   画笔描边的颜色：`ctx.strokeStyle([颜色])`
    -   颜色支持css中的各种颜色格式，包括颜色单词、rgb/rgba、十六进制等
-   填充闭合曲线：`ctx.fill()`
-   填充颜色：`ctx.fillStyle([颜色])`
-   添加新的绘制路径：`ctx.beginPath()`
    -   新绘画路径中设置的样式只在当前路径中生效

## 2.3 绘制直线

-   划线：`ctx.lineTo(x, y)`
-   设置线宽：`ctx.lineWidth = [线宽]`
    -   `[线宽]`：number，像素

```js
// var canvas = document.getElementById("canvasId");
// var ctx = canvas.getContext("2d");
ctx.moveTo(100, 100);
ctx.lineTo(200, 200);
ctx.stroke();
```



## 2.4 绘制矩形（rect）

### 2.4.1 描边矩形

-   先绘制路径，再描边：

    ```js
    // ctx.moveTo(100, 100);
    ctx.rect(100, 100, 300, 300); // (x, y, w, h)
    ctx.stroke();
    ```

-   直接描出矩形：

    ```js
    // ctx.moveTo(100, 100);
    ctx.strokeRect(100, 100, 300, 300);	// (x, y, w, h)
    ```

### 2.4.2 填充矩形

-   线绘制路径，再填充：

    ```js
    // ctx.moveTo(100, 100);
    ctx.rect(100, 100, 300, 300);
    ctx.fill();
    ```
-	直接填充矩形：

	```js
	// ctx.moveTo(100, 100);
	ctx.fillRect(100, 100, 300, 300);
	```

### 2.4.3 清除矩形

-   清除某矩形内的绘制内容，相当于橡皮擦：

    ```js
    ctx.clearRect(x, y, width, height);
    ```



## 2.5 绘制圆弧（arc）

-   `ctx.arc(x, y, radius, startAngle, endAngle, counterClockWise)`

    -   `x, y`：（number）圆心坐标
    -   `radius`：（number）半径
    -   `startAngle` `endAngle`：起始/结束角度，以弧度为单位，如：
        -   30°：`startAngle = 30 * Math.PI / 180`
    -   `counterClockWise`：（boolean）是否是逆时针，默认为``false`

```js
ctx.moveTo(100, 100);
ctx.arc(100, 100, 50, 30*Math.PI/180, -30*Math.PI/180, true);
ctx.closePath();
ctx.stroke();
// ctx.fill();
```



