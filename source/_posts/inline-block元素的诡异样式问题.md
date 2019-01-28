title: inline-block元素的诡异样式问题
date: 2018-07-12 21:49:34
tags:
---
### 记录一次inline-block引起的诡异样式错位
![](cssp.PNG)
想在colorPicker后面加一个icon，点击增加就可以继续增加colorPicker，
用了一个div套i标签，然后设置div为inline-block，但是发现这个div和前面的colorPicker的div怎么都不在一条水平线上。特别尴尬。
同样都是inline-block布局，到底哪里出了问题。
后来仔细看了zxx大佬的博客[CSS深入理解vertical-align和line-height的基友关系](https://www.zhangxinxu.com/wordpress/2015/08/css-deep-understand-vertical-align-and-line-height/)，终于找到了理论基础[w3c可视化格式模型文档](https://www.w3.org/TR/CSS21/visudet.html#propdef-vertical-align)：
```
The baseline of an 'inline-block' is the baseline of its last line box in the normal flow, unless it has either no in-flow line boxes or if its 'overflow' property has a computed value other than 'visible', in which case the baseline is the bottom margin edge.
```
意思就是：
```
一个inline-block元素，如果里面没有inline内联元素，或者overflow不是visible，则该元素的基线就是其margin底边缘，否则，其基线就是元素里面最后一行内联元素的基线
```
这就解释了本文中的两个inline-block元素无法对齐：
因为colorPicker的div里面没有inline内联元素，所以其基线就是margin底边缘。
但是我的icon div盒子里有一个i标签是inline内联元素，所以其基线是i标签内联元素的基线。

zxx博客的这部分内容很好的复现了上述问题。
![](exa.png)

针对博客最后的那个例子我做了几个demo，和博客对比着看。

1. 通过那个规则，改变inline-block基线的位置，从而间距消除
![](a.gif)

2. 通过改变font-size使基线和中线重合
![](b.gif)

3. 通过改变vertical-align的规则，不要基线对齐。从而消除间距
![](c.gif)