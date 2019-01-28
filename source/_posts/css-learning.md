title: css button host style
date: 2015-09-19 14:15:19
tags: css
---
灵感来源于:[Michael Schmid](http://workdiary.de/)

下面就利用CSS3中的transition 和 ::after ::before来制作。

## 效果展示

<!--more-->

![end](http://7xk7fp.com1.z0.glb.clouddn.com/end.gif)


## Turial

###  画一个盒子装button
```
<!DOCTYPE html>
<html>
<head>
	<title></title>
	<style type="text/css">
	.button{
		height: 100px;
		width: 200px;
		border: solid;
		border-color: black;
	}
	</style>
</head>
<body>
<div class="button">button</div>
</body>
</html>
```
![button-start](http://7xk7fp.com1.z0.glb.clouddn.com/button-1.png)

### button调到中间

在button class里加上
```
line-height: 100px;/*单行文字居中的trick*/
text-align: center;
```
![button-center](http://7xk7fp.com1.z0.glb.clouddn.com/button-2.png)

### 利用伪类::after ::before加入hover特效

```
.button::after{
		content: '';/*注意 即使没有内容 也要加上content 否则任何效果都显示不出来*/
		position: absolute;
		top: 0px;
		left: 0px;
		height: 100%;
		width: 1%;/*为了让效果明显 先设置成1%,实际是从0%开始*/
		background: red;
		
}
.button:hover::after{
		width: 100%;/*鼠标悬浮后宽度应变为100%*/
}
```
![button：after](http://7xk7fp.com1.z0.glb.clouddn.com/after.gif)
如果加上过渡效果:
```
.button::after{
		content: '';
		position: absolute;
		top: 0px;
		left: 0px;
		height: 100%;
		width: 1%;
		background: red;
		transition: all 1s;/*为了让效果明显 先设置成1s*/
		
}
.button:hover::after{
		width: 100%;
}
```
加了特效 ,效果duang duang duang:
![button：after](http://7xk7fp.com1.z0.glb.clouddn.com/transition.gif)

### 为什么在整个body里？
为什么不是在button内部？

因为absolute布局相对的父元素是上一级的relative 或者 absolute布局，button class里我们没有为他布局，相当默认的static，所以他直接相对于body，top:0;left:0;都是相对body的。因为body默认本身就是一个relative
的布局。当我们为.button加上一个position:relative时：
```
.button{
		position: relative;/*here！*/
		height: 100px;
		width: 200px;
		border: solid;
		border-color: black;
		line-height: 100px;
		text-align: center;
	}
```
效果就出来了:

![button：after](http://7xk7fp.com1.z0.glb.clouddn.com/button-content.gif)

### 文字被遮住了

怎么解决文字被遮住的问题？
想到了用z-index，设置元素的堆叠顺序。默认是0，如果能让button比0高，不就可以一直在上面了。
```
#button{
		z-index: 1;
}

<div class="button"><span id="button">button</span></div>
```
然而效果没有改变：

![button：after](http://7xk7fp.com1.z0.glb.clouddn.com/button-content.gif)

当:
```
#button{
		position:relative;/*here!*/
		z-index: 1;
}

<div class="button"><span id="button">button</span></div>
```
效果就实现了。为什么加了一个position就实现了？请阅读：[CSS z-index 属性的使用方法和层级树的概念](http://www.neoease.com/css-z-index-property-and-layering-tree/)
![button：after](http://7xk7fp.com1.z0.glb.clouddn.com/label.gif)

## 结语
具体的色调 字体 大小 背景色就留给大家设计吧。
