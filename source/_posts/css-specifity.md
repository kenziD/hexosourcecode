title: CSS 权重
date: 2016-03-24 19:53:48
tags: css
---

对css权重的概念理解的并不深入，很多是流于表面。先从css权重讲起吧。

本文参考了：
[你应该知道的一些事情——CSS权重](http://www.w3cplus.com/css/css-specificity-things-you-should-know.html)

<!--more-->
例如
```
html:
<div class="a" id="b"></div>
css:
div.a#b {}
div#b.a {}
.a#b {}
#b.a {}
div#b {}
#b {}
div.a {}
.a {}
div {}
```
哪个权重比较大？一般都知道!important>内嵌样式表>#id>.class>tag,那么组合起来又该如何比较。

回来后经过一番实践。
权重就是刚才顺序的从上到下，由大到小。
给我的感性认识是：

* 含有id选择器的权重总比没含的要大，即，如果一条规则包含了更高权重的选择器，那么这个规则权重更高。
* 不过有谁会去使用这种多层的选择器呢？很明显id本来就是独一无二的。

理性的认识：

* 原来权重的计算还有一套公式的。
* 选择器规则的优化

下面摘录一些我认为比较重要的知识点：

### 权重的等级分类

分为四类：

##### 行内样式，指的是html文档中定义的style

权重：1000

##### ID选择器

权重：100

##### 类，属性选择器和伪类选择器

权重：010

[属性选择器](http://www.w3cplus.com/css3/attribute-selectors)就是div[style="xxxxx"]


[伪类选择器有哪些？](http://www.w3cplus.com/css3/pseudo-class-selector)

##### 元素和伪元素

权重：001


[伪元素有哪些？](http://www.w3cplus.com/css3/pseudo-class-selector)


总的权重值计算方法就是：

从0开始，一个行内样式+1000，一个id+100，一个属性选择器、class或者伪类+10，一个元素名或者伪元素+1.

另一种方法：计算有几个id选择器的数量为a ,计算其他属性跟class选择器的数量为b ，计算元素名跟伪元素名的数量为c ，然后结合起来就是权重：a,b,c。

所以刚才题目的权重是可以算出来的：

```
	/*1:111*/ 
    div.a#b {}

    /*2:111*/
    div#b.a {}

    /*3:110*/
    .a#b {}

    /*4:110*/
    /*exmaple:#btn_specific.active*/
    #b.a {}

    /*5:101*/
    div#b {}

    /*6 :100*/
    #b {}

    /*7:011*/
    /*这种写法感觉就是为了语义明确把，比如a.text-primary:hover(bootstrap)*/
    div.a {}

    /*8:010*/
    .a {}

    /*9:001*/
    div {}
```

by the way ,多个class的权重都是一致的。谁离的最近谁优先级最高。
比如
```
<div class="c d"></div>
css:
.c.d{}
.d.c{}
```
谁放在后面就会显示谁,因为权重一样。

如果足够多的低权重选择器累加到＝高权重选择器，权重就会反超。具体请戳

[有趣：256个class选择器可以干掉1个id选择器](http://www.zhangxinxu.com/wordpress/2012/08/256-class-selector-beat-id-selector/)

具体浏览器的对css权重的判断策略，大家可以去看看源代码。

这里是[火狐](http://hg.mozilla.org/mozilla-central/file/tip/layout/style/StyleRule.cpp#l479)的。

这里是[webkit](http://trac.webkit.org/browser/trunk/Source/WebCore/css/CSSSelector.cpp?rev=81845#L63)的

大致就是，tag的＋1，id＋0x010000,class+0x000100

[W3C标准计算css权重](https://www.w3.org/TR/css3-selectors/#specificity) 

[可视化计算css权重](https://specificity.keegan.st/)


### CSS选择器的优化

[编写高效的 CSS](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Writing_efficient_CSS)

总的来说就是越具体的放在后面，少用子代选择器，少用后代选择器。id后不要再加类名或者tag，（例外：有一些会用js变动的class名）

多用继承。

[Writing efficient CSS selectors](http://csswizardry.com/2011/09/writing-efficient-css-selectors/)
[CSS选择器的优化](http://www.w3cplus.com/css/css-selector-performance)