title: 图表库比较
date: 2016-03-14 15:51:31
tags:
---
## 综述
### [HIGHTCHARTS](http://www.highcharts.com/)

####特点

* highcharts可以运行在任何现代浏览器，包括移动终端以及IE6，标准的浏览器用SVG技术渲染图表，对于遗留的浏览器，则用VML来绘图。

* 分为三个js:HIGHCHARTS(图表),HIGHSTOCK(针对股票),HIGHMAPS(针对地图)

* 基于JQuery

#### 优点

* API手册非常详细，例子也非常详细。设置格式和Echarts差不多。每个设置项都有Jsfiddle的例子，可以直观快速的明白。
* 比较成熟，有免费的和收费的。例如Twitter,Facebook都在使用.

#### 缺点

* 体积比较大

<!--more-->
### [Echarts](http://echarts.baidu.com/)

#### 特点

* 不支持IE8或以下的
* 使用原生JavaScript

#### 优点：

* 0学习曲线，易上手。
* 中文资料丰富。
* 基础的图表就已经比较美观。
* 使用原生JavaScript
* 支持自定义构建，像bootstrap那样可以选择自己需要的图表，然后集成一个js包。

#### 缺点：

* 灵活性有限。


### [D3](https://d3js.org/)

#### 特点：

* 类似于JQuery，使用了链式语法，有自己的选择器。一个函数套一个函数。

* 仅支持现代浏览器，对IE9或以下版本支持不是很好

#### 缺点：

* 学习成本比较高，学习曲线比较陡.
* 官方文档写得不好，api手册几乎没有例子,很难懂。

#### 优点：

* 基于svg格式，放大无失真。
* 自主选项多，自由度很大，可以做出很丰富的图表类型。

##加载速度

由于加载速度与网络情况和服务器处理有很大相关，这里只是本地加载，不作测试。

##内存消耗


### HIGHCHARTS

![](http://7xk7fp.com1.z0.glb.clouddn.com/highchart.png)

![](http://7xk7fp.com1.z0.glb.clouddn.com/highchart-storage.png)

内存：11.1M

加载对象：highcharts.js jquery-1.8.3.min.js

### Echarts

![](http://7xk7fp.com1.z0.glb.clouddn.com/echarts.png)

![](http://7xk7fp.com1.z0.glb.clouddn.com/echarts-storage.png)

内存：10.4M

加载对象：echarts.min.js 

### D3

![](http://7xk7fp.com1.z0.glb.clouddn.com/d3.png)

![](http://7xk7fp.com1.z0.glb.clouddn.com/d3-storage.png)

内存：9.8M

加载对象：d3.js

## chrome 一个tab页面的内存

![](http://7xk7fp.com1.z0.glb.clouddn.com/chrome-storage.png)