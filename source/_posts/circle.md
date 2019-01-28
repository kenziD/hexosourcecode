title: 纯CSS3制作动态饼状图
date: 2015-10-19 17:34:12
tags: css
---
# [Demo](http://jsfiddle.net/0m20xaLx/1/)

刚开始在想如何画圆环，想着用两个Div嵌套，还一直研究如何使圆心居中，实际上就是如何是子div垂直水平居中，也算误打误撞学了一下知识。

看了教程才意识到只需一个div设置一下边框宽度就可以画出圆环了。

html:
```
<div class="circle"></div>

```
<!--more-->
首先利用:before要画一个半圆:

![](http://7xk7fp.com1.z0.glb.clouddn.com/half-circle.png)

```
.circle:before{
			box-sizing:border-box;
			content: "";
			position: absolute;
			top: 0;
			left: 0;
			width: 50px;
			height: 100px;
			border-radius: 50px 0 0 50px;/*画半圆*/
			background:#0cf;
		}
```
画半圆的关键是设置border-radius的大小和宽一致,以及宽度是高度的一半。
```
border-radius: 20px 0 0 20px;
```
![](http://7xk7fp.com1.z0.glb.clouddn.com/radius-1.png)

```
border-radius: 30px 0 0 30px;
```
![](http://7xk7fp.com1.z0.glb.clouddn.com/radius-2.png)

利用div本身的遮罩实现0度到180度的变换：

```
.circle{
			box-sizing:border-box;
			width: 50px;
			height: 100px;
			background:rgba(0,0,0,0.2);/*为了看清楚，先随意设一个颜色*/
			position: relative;
	}
		.circle:before{
			box-sizing:border-box;
			content: "";
			position: absolute;
			z-index: -1;/*遮罩应该位于圆的上层，覆盖掉圆*/
			top: 0;
			left: 0;
			width: 50px;
			height: 100px;
			border-radius: 50px 0 0 50px;
			background:#0cf;
			transform:rotate(30deg);/*旋转30度*/
			transform-origin:50px 50px;/*旋转的圆心的位置应该位于直径的中点*/
		}
```
![](http://7xk7fp.com1.z0.glb.clouddn.com/deg.png)
如果动态改变角度，就可以实现：

```
.circle:before{
			box-sizing:border-box;
			content: "";
			position: absolute;
			z-index: -1;/*遮罩应该位于园的上层，覆盖掉园*/
			top: 0;
			left: 0;
			width: 50px;
			height: 100px;
			background:#0cf;
			transform:rotate(0deg);
			transform-origin:50px 50px;
			animation:myRotate-before 2s linear;/*自定义动画*/
			-webkit-animation:myRotate-before 2s linear;
		}
		@-webkit-keyframes myRotate-before /* Safari and Chrome */
		{
		from {transform:rotate(0deg);}/*动画的实现是从0度转到180度*/
		to {transform:rotate(180deg);}
		}
```
![](http://7xk7fp.com1.z0.glb.clouddn.com/a.gif)

利用:after为元素右侧也画一个半圆，代码部分的变动只是把刚才的半圆旋转到180度而已
```
.circle:after{
			box-sizing:border-box;
			content: "";
			position: absolute;
			top: 0;
			left: 0;
			width: 50px;
			height: 100px;
			border-radius: 50px 0 0 50px;
			background:rgba(30,144,255,0.2);
			transform:rotate(180deg);
			transform-origin:50px 50px;
		}
```

![](http://7xk7fp.com1.z0.glb.clouddn.com/circle2.png)
如果将:before半圆旋转270度，与：after半圆重合起来看就是一个角度为270的圆。刚开始设置其属性为opacity：0，等到:before的半圆转到180后，将其opacity设为1，让这个躲在后面的圆显现出来即可。还有一点就是左边的遮罩在before半圆旋转范围在0-180时应该一直保持遮罩，但在180-360时就应该将其背景色调为transparent，使得：before半圆能够在180-360的旋转过程中完整显现出来。
![](http://7xk7fp.com1.z0.glb.clouddn.com/circle3.png)
完整的css代码如下：
```
.circle{
			box-sizing:border-box;
			width: 50px;
			height: 100px;
			background:white;
			position: relative;
			animation:myRotate 2s  linear forwards;/*forward的用处是让动画停止在最后1帧*/
			-webkit-animation:myRotate 2s  linear forwards;
		}
		.circle:before,.circle:after{
			box-sizing:border-box;
			content: "";
			position: absolute;
			z-index: -1;
			top: 0;
			left: 0;
			width: 50px;
			height: 100px;
			border: solid;
			border-width:10px 0 10px 10px;/*画圆环*/
			border-color:#0cf;
			border-radius: 50px 0 0 50px;/*画半圆*/
			background:white;
		}
		.circle:before{
			transform:rotate(0deg);
			transform-origin:50px 50px;
			animation:myRotate-before 2s  linear forwards;
			-webkit-animation:myRotate-before 2s  linear forwards;
		}
		.circle:after{
			transform:rotate(180deg);
			transform-origin:50px 50px;
			animation:myRotate-after 2s linear forwards;
			-webkit-animation:myRotate-after 2s  linear forwards;
			opacity: 0;
		}
		@-webkit-keyframes myRotate-before /* Safari and Chrome */
		{
		from {transform:rotate(0deg);}
		to {transform:rotate(320deg);}/*可以自己调整*/
		}
		@-webkit-keyframes myRotate-after  /*Safari and Chrome*/
		{
		0%{
		    opacity:0;
		  }
		  55%
		  {
		    opacity:0;
		  }
		  56%
		  {
		    opacity:1;
		  }
		  100%
		  {
		    opacity:1;
		  }
		}
		@-webkit-keyframes myRotate  /*Safari and Chrome*/
		{
		0%{
		   background:white;
		  }
		  56%
		  {
		    background:white;
		  }
		  57%
		  {
		    background:transparent;
		  }
		  100%
		  {
		    background:transparent;
		  }
		}
```
![](http://7xk7fp.com1.z0.glb.clouddn.com/final.gif)
参考的这篇文章:[純 CSS 繪製圓餅圖](http://www.oxxostudio.tw/articles/201503/css-pie-chart.html)

