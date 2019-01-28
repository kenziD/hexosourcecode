title: json数据要用双引号
date: 2016-03-31 14:24:47
tags: Javascript
---
今天遇到了一个json的坑，原来json里的字符串必须是双引号，我用单引号，sublime就提示高亮的错误，而且jquery的
$.get()或者 $.getJSON()都没反应，调不到数据。改成双引号就一切ok了
[json 官方网站](http://www.json.org/index.html)也是这么说的(double quote!)

<!--more-->
又遇到了一个坑，原来json里不能有注释的东西。

由于jQuery使用get请求获取json data的时候出错了也不提示的。静默错误。

> it will fail silently unless the script has also called the global .ajaxError() method

jQuery.get()会返回一个jqXHR对象，在1.5中是包含jqXHR.success(), jqXHR.error(), and jqXHR.complete()三个方法，其中jqXHR.error()可以处理错误，但是1.8之后都替换成了jqXHR.done(), jqXHR.fail(), and jqXHR.always()方法，其中jqXHR.fail()用来处理错误。

```
var jqxhr = $.get( "example.json", function() {
  alert( "success" );
})
 .fail(function(error) {
    alert(error);
  })
```

可以看出错误的信息。

不过最好用一些在线的json工具检查一下是否有错，这样就不会像无头苍蝇一样了。比如[jsonlint](http://jsonlint.com/)

gulp也有相关的[测试插件](https://www.npmjs.com/package/gulp-json-lint)


```
$.get(function(){
	//success1!
}).done(function(){
	//success2!
})
```
有什么区别？

为了搞清楚这个问题，不得不去看了javascript的各种回调方法。

jquery本来的方法是用监听事件来实现，这里的get方法是底层ajax的简写。监听了success事件，然后才来执行success1
```
$.ajax({
  url: url,
  data: data,
  success: success,
  dataType: dataType
});
```

后来又加了一个[Deferred()](http://api.jquery.com/jQuery.Deferred/)方法，是基于CommonJS Promises/A 设计的，返回一个promise对象（对应jqXHR对象），promise对象会有一个then方法（对应done方法），可以在异步任务完成后，才执行回调函数。所以两个都保留了下来。

阮一峰的博客逻辑很清楚。[avascript异步编程的4种方法](http://www.ruanyifeng.com/blog/2012/12/asynchronous%EF%BC%BFjavascript.html)

## json的知识点

要区分清楚json对象，和json文本

```
 var str= '{ "name": "张三", "sex": "男" }';   //文本

 var obj=str.parseJSON();   //把其Json文本解析成为json对象   

 alert(obj.toJSONString()); //把json对象转换成JSON 文本  
```

所有highchart的函数都要写在$(function(){
	.....
})
里。

```
$(function(){
	//
})
```
等价于
```
jQuery(function() { 
	//	
})
```
等价于
```
$(document).ready(funtion(){
	//
})
```
的缩写。
```

jQuery(function($){
	//最安全的写法。$作为局部变量。这样就不会和外面定义的$混淆了。
})
```
