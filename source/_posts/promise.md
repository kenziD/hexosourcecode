title: 初探promise
date: 2016-05-08 22:37:59
tags: Javascript
---
## 插件

今天实现了一个插件的动画，就是纸从折叠到展开的效果。主要用css3的tansition实现。js监听第一个列表rotate结束(监听transitionend事件)，然后开始第二个rotate开始，transition结束后，再开始第三个的展开。回调地狱有木有。于是学了一下promise规范。发现es6已经有了原生的方法。就没有用库。看了回调地狱的官网(居然有官网)，感觉概念清晰了很多。按照里面的方法重构了代码。看起来清晰多了。

<!--more-->

```
function folderPaper() {
    var otherLi = document.querySelectorAll('.colorBtnList li:nth-child(n+2)');

    function animation1() {
        var animate = new Promise(function(resolve, reject) {
            otherLi[0].style.display = "block";
            window.setTimeout(function() {
                // this has to be add id .if add class,the preority are small then css3 li:nth-child selector.transition will not work
                otherLi[0].setAttribute("id", "display0deg");
                otherLi[0].addEventListener("transitionend", function() {
                    resolve(0);
                });
            }, 100);

        });
        return animate
    }

    function animation2() {
        var animate = new Promise(function(resolve, reject) {
            otherLi[1].style.display = "block";
            window.setTimeout(function() {
                // this has to be add id .if add class,the preority are small then css3 li:nth-child selector.transition will not work
                otherLi[1].setAttribute("id", "display0deg");
                otherLi[1].addEventListener("transitionend", function() {
                    resolve(1);
                });
            }, 5);

        });
        return animate
    }

    function animation3() {
        var animate = new Promise(function(resolve, reject) {
            otherLi[2].style.display = "block";
            window.setTimeout(function() {
                // this has to be add id .if add class,the preority are small then css3 li:nth-child selector.transition will not work
                otherLi[2].setAttribute("id", "display0deg");
                otherLi[2].addEventListener("transitionend", function() {
                    resolve(2);
                });
            }, 5);

        });
        return animate
    }

    animation1().then(animation2).then(animation3);
    // Promise.all([animation1(0), animation1(1), animation1(2)]);
}

```
不过你应该也发现代码有一堆重复的部分。
于是我继续重构
```
function folderPaper() {
    var otherLi = document.querySelectorAll('.colorBtnList li:nth-child(n+2)');

    function animation(index) {
        var animate = new Promise(function(resolve, reject) {
            otherLi[index].style.display = "block";
            window.setTimeout(function() {
                // this has to be add id .if add class,the preority are small then css3 li:nth-child selector.transition will not work
                otherLi[index].setAttribute("id", "display0deg");
                otherLi[index].addEventListener("transitionend", function() {
                    console.log("over");
                    //关键
                    resolve(index+1);
                });
            }, 100);

        });
        return animate
    }

    animation(0).then(animation).then(animation)

}

```
清晰多了有木有。

后来我尝试了promise.all方法，结果发现promise.all不能一个接一个，而是同时执行的。仍需探索。后续补充。

## 参考
[javascript异步编程原理](http://www.cnblogs.com/hustskyking/p/javascript-asynchronous-programming.html)
[异步编程：When.js快速上手](https://imququ.com/post/promises-when-js.html)
[Callback Hell](http://callbackhell.com/)
[Javascript Promise 迷你书](http://liubin.org/promises-book/#introduction)