title: 立即调用函数表达式
date: 2016-03-20 22:26:11
tags:
---
我第一次见到
```
(function($){
	
})(jQuery);

```
并不理解这段程序的意思。That's why I write this blog.

<!--more-->
IIFE="Immediately-Invoked Function Expression"
立即调用函数表达式

有些人称之为 “self-executing anonymous function”
自执行匿名函数

它的形式是：
```
(function (){
	//something
}());
```
or

```
(function (){
	//something
})();
```

这里面的函数是一个匿名函数。后面的括号表示对函数的调用(执行)里面可以传入实参。另外一个括号表示分组运算符。要想彻底理解要先搞清楚一些概念。

### 函数声明

匿名函数的声明

```
function (){
	//something
};
```
具名函数的声明

```
function a(){
	//something
};
a()

```
他们是statement,不是expression。他无法在后面加一个()就可以执行。比如
```
function (){
	//something
}();//Uncaught SyntaxError: Unexpected token (

function a(){
	//something
}();//Uncaught SyntaxError: Unexpected token )
```

第一个提示错误(，是因为这里的括号是作为函数或对象方法的调用运算符。他是匿名函数，没有函数名，所以报错了。

第二个有函数名了，为啥还报错，因为这时后面的()是作为[分组运算符](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Grouping)。分组运算符里面的内容为空，所以运行到)就报错了。

如果换成
```
function a(){
	console.log("call a");
}(1);
```
就可以运行了，因为这时分组运算符里面不为空啦。
```
function a(){
	console.log("call a");
}(alert("haha"));

```
也是一样的，先执行警告，然后声明这个函数。你会发现并没有执行这个函数a().



### 函数表达式

函数表达式中的函数可以为匿名函数，也可以有函数名，但是该函数实际上不能直接使用，只能通过表达式左边的变量 a 来调用。

将匿名函数放在一个赋值表达式的右边

```
var a = function(){
	//something
};
a();
```
将具名函数放在一个赋值表达式的右边

```
var a = function b(){
	//something
};
a();
b();//这样是错误的
```
刚才的函数声明不能直接加()运行，现在是函数表达式了，现在就可以直接加()运行了。
```
var a = function b(){
	//something
}();
```
函数声明非常容易转换为函数表达式:

* 成为表达式的一部分(如上例就是赋值表达式的一部分)
* 不再是函数或者script自身的“源元素” （source element）。在script或者函数体内“源元素”并非是内嵌的语句（statement）




所以加上一切能使其成为函数表达式的[运算符](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators)后，后面都可以加一个()来立即执行调用这个函数。
如，所有的一元运算符

```
! function b() {
    console.log('called b');
}(); 

- function b() {
    console.log('called b')
}();

+ function b() {
    console.log('called b')
}();

~ function b() {
    console.log('called b')
}();

typeof function b() {
    console.log('called b')
}();

delete function b() {
    console.log('called b')
}();

void function b() {
    console.log('called b')
}();

```
还有：
```
true ||function b() {
    console.log('called b')
}();
[function () {
    console.log('called b')
}()];
new function b() {
    console.log('called b')
}();
```
不过像!,+,-都会对函数返回的结果有计算，所以直接用一个分组运算符，对函数不会造成影响。
```
console.log(-function b() {
        console.log('called b');
        return 3;
}());//result:-3
console.log(~function b() {
        console.log('called b');
        return 3;
}());//result:-4
```
回到最开始的问题。
```
// Code 1:
(function ($) { 
    // Javascript code 
})(jQuery)

// 相当于:
var myFunction = function ($) { 
    // Javascript code 
};
(myFuntion)(jQuery);//第一个括号是分组运算符，第二个括号表执行。
//不过这里的myFunction可以直接执行。因为它已经是表达式了，无需再添加一个分组运算符转换成表达式。
myFunction(jQuery);
```
其实是将$作为形参，jQuery 作为实参传入，并且是一个立即执行的函数表达式。

## 结语

本文参考了

[深入理解JavaScript系列（4）：立即调用的函数表达式](http://www.cnblogs.com/TomXu/archive/2011/12/31/2289423.html)

[JavaScript 匿名函数有哪几种执行方式?](https://www.zhihu.com/question/20249179)

[JavaScript中的立即执行函数](https://segmentfault.com/a/1190000003902899)

[js中(function(){…})()立即执行函数写法理解](http://dengo.org/archives/1004)

[原文](http://benalman.com/news/2010/11/immediately-invoked-function-expression/#iife)