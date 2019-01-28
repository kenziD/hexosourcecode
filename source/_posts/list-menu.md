title: 制作input下拉菜单
date: 2015-10-12 16:23:42
tags: 
- css
- Javascript
---
##[Demo](https://jsfiddle.net/17wgtvp1/)

##画出输入框和图标

<style type="text/css">
* {
    padding: 0;
    margin: 0;
    box-sizing:border-box;
}

input {
    border: 0;
}

#input-area {
	border: 1px solid #e6e8e9;
	 padding: 0 10px;
height: 30px;
width: 150px;
}
#last-input-area {
    border: 1px solid #e6e8e9;
    padding: 0 10px;
    height: 30px;
    width: 150px;
}
#final-input-area {
    border: 1px solid #e6e8e9;
    padding: 0 10px;
    height: 30px;
    width: 150px;
}
#icon {
	
    width: 30px;
    height: 30px;
    background: url(http://ww4.sinaimg.cn/large/74311666jw1ewyg87xugqj2074074wel.jpg) no-repeat;
    background-size: contain;
    cursor: pointer;
    /*好厉害*/
}
   #menu{
        border:1px solid #e6e8e9;
       
        width: 150px;
        background: white;
          margin-left: -0.5px;
        margin-top: -22px;
    }
#last-menu{
position:absolute;
display:none;
        border:1px solid #e6e8e9;
        width: 150px;
        background: white;
          margin-left: -0.5px;
        margin-top: -22px;
    }
    #final-menu{
position:absolute;
display:none;
        border:1px solid #e6e8e9;
       
        width: 150px;
        background: white;
          margin-left: -0.5px;
        margin-top: -22px;
    }
.list-menu{
    
    font-size: 10px;
    padding: 10px 0;
    list-style-type: none;
    }
   .final-list-menu{
    
    font-size: 10px;
    padding: 10px 0;
    list-style-type: none;
  
}
.list-menu:hover{
    background: #eee;
}
.final-list-menu:hover{
    background: #eee;
}
</style>


####消除两个元素之间的空格
一般情况下，写html时都是有自动换行的：就像这样
<!-- more-->
```
<input id="input-area" type='text' placeholder="输入你想搜索的内容" >
<input id="icon" />
```
CSS:
```
* {
    padding: 0;
    margin: 0;
    box-sizing:border-box;
}

input {
    border: 0;
}

#input-area {
    border: 1px solid #e6e8e9;
    padding: 0 10px;
    height: 30px;
    width: 150px;
}

#icon {
    width: 30px;
    height: 30px;
    background: url(http://ww4.sinaimg.cn/large/74311666jw1ewyg87xugqj2074074wel.jpg) no-repeat;
    background-size: contain;
}
```
这时出来的效果是：
<input id="input-area" type='text' placeholder="输入你想搜索的内容" >
<input id="icon" style="cursor: default;"/>

解决办法是：
```
<input id="input-area" type='text' placeholder="输入你想搜索的内容" ><!--
--><input id="icon" />
```
这样就可以消除两行元素的空格变成在一行内。

<input id="input-area" type='text' placeholder="输入你想搜索的内容" ><!--
--><input id="icon" style="cursor: default;"/>
####如何隐藏光标

你会发现上面的icon图标会有光标显现，如何隐藏光标呢？

将icon的color设为tranparent即可。前提是你不需要在这个input内输入任何内容：
```
#icon {
    color:tranparent;//隐藏光标
    width: 30px;
    height: 30px;
    background: url(http://ww4.sinaimg.cn/large/74311666jw1ewyg87xugqj2074074wel.jpg) no-repeat;
    background-size: contain;
}
```
<input id="input-area" type='text' placeholder="输入你想搜索的内容" ><!--
--><input id="icon" style="cursor: default;color:transparent;"/>

####如何让鼠标显示出链接选择样式

作为icon的搜索图片，应该点击图片就能有搜索button的作用，但是鼠标移到上面时还是箭头，怎么办？要在外面套一个a标签吗？
更便捷的方法是，在CSS属性上加上:

```
#icon {
    color:tranparent;
    width: 30px;
    height: 30px;
    background: url(http://ww4.sinaimg.cn/large/74311666jw1ewyg87xugqj2074074wel.jpg) no-repeat;
    background-size: contain;
    cursor:pointer;//改变鼠标样式
}
```
<input id="input-area" type='text' placeholder="输入你想搜索的内容" ><!--
--><input id="icon" style="cursor: default;color:transparent;cursor:pointer;"/>

可以用Jquery的click事件进行链接。

##制作下拉菜单

```
<input id="input-area" type='text' placeholder="输入你想搜索的内容" ><!--
--><input id="icon" />

<ul id="menu">
    <li class="list-menu list1">css课程</li>
    <li class="list-menu list2">javascript课程</li>
    <li class="list-menu list3">linux课程</li>
    <li class="list-menu list4">R课程</li>
    <li class="list-menu list5">C++课程</li>
</ul>
```
CSS:
```
 #menu{
        border:1px solid #e6e8e9;
        position: absolute;
        width: 150px;
        background: white;
      
    }
.list-menu{
    
    font-size: 10px;
    padding: 10px 0;
    list-style-type: none;
    
}
.list-menu:hover{
    background: #eee;
}
```

 <input id="input-area" type='text' placeholder="输入你想搜索的内容" ><input id="icon" style="color:transparent;"/><!-- 
    --><ul id="menu">
     <li class="list-menu list1">css课程</li><!-- 
     --><li class="list-menu list2">javascript课程</li><!-- 
     --><li class="list-menu list3">linux课程</li><!-- 
     --><li class="list-menu list4">R课程</li><!-- 
     --><li class="list-menu list5">C++课程</li>

##点击输入框时下拉菜单出现 点击其他位置下拉菜单隐藏

一般情况下，下拉菜单应该是隐藏的，鼠标点击到输入框时浮现，点击网页的其他地方，下拉框消失。这需要用到JS和Jquery库。

####点击输入框下拉框浮现

首先要在#menu加上display:none

```
#menu{
        display: none;
        border:1px solid #e6e8e9;
        position: absolute;
        width: 150px;
        background: white;
    }
```
接下来实现点击input时display变为block即可。

我最先想到的是
Javascript:
```
   $("#input-area").click(function() {
        $("#menu").css("display","block");
        // body...
    })
```

但如何在其他地方点击，能够隐藏menu呢？
最终参考了[Jquery的例程](https://api.jquery.com/event.target/)
```
function handler( event ) {
  var target = $( event.target );
 
  if ( target.is( $("#input-area"))  ) {
    $("#menu").show();
  }
  else
    $("#menu").hide();
}
$("html").click(handler);

```
注意：hide(),show()函数只针对dispaly:none,对于visibility:hidden没有用。
<input id="last-input-area" type='text' placeholder="输入你想搜索的内容" /><input id="icon" style="color:transparent;"/><!-- 
--><ul id="last-menu">
 <li class="list-menu ">css课程</li><!-- 
 --><li class="list-menu ">javascript课程</li><!-- 
 --><li class="list-menu ">linux课程</li><!-- 
 --><li class="list-menu ">R课程</li><!-- 
 --><li class="list-menu ">C++课程</li>
 </ul>

顺便这里隆重推出absolute的用法，你会发现我的下拉菜单是覆盖在页面上的，如何实现的这个效果呢？直接在#menu中加上position:absolute属性即可，且父元素不要加position：relative.好神奇！其实我这篇文章也是根据这个课程引出的：[absolute深入理解](http://www.imooc.com/video/4462)
##鼠标选择下拉菜单选项填入input框内

鼠标点击li标签的内容，返回其文本：
```
$(".list-menu").click(function(e){
console.log(e.target.innerHTML);
```
其中e.target.innerHTML就是其文本。


并将文本传入input框内：
```
$(".list-menu").click(function(e){
console.log(e.target.innerHTML);
 document.getElementById("input-area").value = e.target.innerHTML;
});
```
> 这里注意：Jquery的$("#id")和document.getElemenetById("id")是不完全一样的。document.getElemenetById("id")获得的直接是DOM对象，
而Jquery的$("#id")返回的是Jquery对象，是一个数组，用console.log()看一下就清楚了。
$("#id") = document.getElemenetById("id")。

我是通过$("#input-area").value无法使用发现的。如果改成$("#input-area")[0].value就正确了

<input id="final-input-area" type='text' placeholder="输入你想搜索的内容" /><input id="icon" style="color:transparent;"/><!-- 
--><ul id="final-menu">
 <li class="final-list-menu ">css课程</li><!-- 
 --><li class="final-list-menu">javascript课程</li><!-- 
 --><li class="final-list-menu">linux课程</li><!-- 
 --><li class="final-list-menu">R课程</li><!-- 
 --><li class="final-list-menu">C++课程</li>
 </ul>
