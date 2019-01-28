title: Scroll with Javascript VS CSS3
date: 2015-10-02 00:43:04
tags:
---
实现点击button滑动到指定页面的特效。

应用:

* 点击导航栏滑动到指定的页面
* 点击MORE就往下滑动一个页面
* 点击*回到顶部*就滚回到首页

<!--more-->
这里分别用

* Javascript：
	* window.scrollBy()控制滚动条实现
	[代码+Demo](https://jsfiddle.net/g1760uno/)
	> 缺点：感觉不够顺滑流畅且没有动画特效
	> 如果有大背景图片，就不够流畅了。更别提fixed。

	* Jquery库里的.animate实现
	[代码+Demo](https://jsfiddle.net/yugko0c6/)
	> 优点：相对顺滑流畅 且有动画特效
	> 缺点：如果有大背景图片，就不够流畅了。更别提fixed。

* CSS3里的transition：
[代码+Demo](https://jsfiddle.net/wk7e4zep/)
> 优点：非常顺滑流畅！有动画特效。即使是有大背景图片。
> 缺点：滚动条失效。因为是使用tranlateY使其整体上移下移，和滚动条无关。当 然也就无法实现fixed效果
> 其实这里的实现方法，感觉不完美。
> 代码中多写了两个id分别为page_1和page_2的div，来对应a标签中的href，只是为了能在触发target的时候，能让后面的所有为wrap类的容器一起执行transform这样page1与page2才不会断层。
> 我认为更好的解决方式是，不要另起id名，直接就用page1 page2,然后在
article外面套一个#main的div，触发page1和page2时能选择父元素#main使其整体上升下降。但是CSS3里貌似没有父级元素选择器，有了貌似浏览器也米有支持。具体可以google.

## 3种实现方式一起比较:[Demo](https://jsfiddle.net/a4aoshp9/)

这里再记录一下关于Javascript里的原生onclick()和JQuery里的.click(funtciton(){}).我的代码里都用的是onclick，不过对比会发现，使用.click更流畅，具体比较请看：[jQuery.click() vs onClick](http://stackoverflow.com/questions/12627443/jquery-click-vs-onclick)