<h1 style="fontsize:28px;text-align:center;font-family:Georgia;font-style:italic;color:#333"”>Bootstrap Notes</h1>
[TOC]

# Bootstrap CSS

## Bootstrap 网格系统（Grid System）

-   响应式、移动设备优先、不固定
    -   内容：决定什么是最重要的
    -   布局：
        -   优先设计更小的宽度
        -   基础的CSS是移动设备优先，媒体查询是针对平板电脑、台式电脑
    -   渐进增强：
        -   随着屏幕大小的增加而添加元素
-   可以随着设备或视口大小的增加而适当的扩展到12列



## Bootstrap 网格系统的工作原理

网格系统通过一系列**包含内容的行和列**来创建页面布局。

-   行必须放在`.container` class内，以便获得适当的对齐（alignment）和内边距（padding）
-   使用行来创建列的水平组
-   内容应该放置在列内，并且唯有列可以是直接子元素
-   预定义的网格类，比如`.row`和 `.col-xs-4`，可用于快速创建网格布局
-   列通过内边距（padding）来创建列内容之间的间隙。