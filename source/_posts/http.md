title: http协议学习笔记
date: 2016-05-14 15:38:48
tags: protocal
---
有部分内容来自[Unacceptable Browser HTTP Accept Headers (Yes, You Safari and Internet Explorer)](http://www.newmediacampaigns.com/blog/browser-rest-http-accept-headers)

## 报头详解

### Accept 深入理解
属于请求头。客户端对服务端的协商。

表示接受的 MIME type.

MIME(Multipurpose Internet Mail Extensions) 表示多功能 Internet 邮件扩充服务。本来是用来定义邮件接受的媒体类型。后来被应用到http协议上后，内容就变得多种多样了。一共有七种
text,img,video,audio,multipart,application,
其中他们还有子类。比如text/html,text/plain,img/jpeg,img/gif等。

格式为：\*/\*;q=float 用分号分隔; 默认情况为q=1。q表示用户对某种类型的接受程度。

比如：chrome 浏览器下：
```
GET /Protocols/rfc2616/rfc2616-sec7.html HTTP/1.1
Host: www.w3.org
Accept:text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
```
表示:

如果有text/html image/webp application/xhtml+xml这些格式最好。100%

如果上面的都没有，给我xml格式。90%

如果还是没有就给我啥都行。80%

再比如：
```
Accept: text/*;q=0.3, text/html;q=0.7, text/html;level=1,text/html;level=2;q=0.4, */*;q=0.5
```
text/html;level=1         = 1

text/html                 = 0.7

text/plain                = 0.3

image/jpeg                = 0.5

text/html;level=2         = 0.4

text/html;level=3         = 0.7

我在想，请求行里的url的扩展里已经包括了资源的类型:.html,.jpeg等，为什么还要通过accept告诉服务器自己接受什么类型的文件呢？猜想直接指明类型会更快的响应请求。省的解析url里的类型。也有的不一定就能返回特定的指定类型，如果没有，也可以用其他类型代替。有一定的容错性。而且不一定url的请求就是一个指定的文件，可能只是某个文件夹。

不过听说，这些服务商都不用accept：

> As stated, Twitter's REST API doesn't use the Accept header for content-negotiation, they use extensions on the URL '.json' and '.xml'.

> Rails disables the Accept header by default. 

> Frameworks can enhance performance by ignoring the Accept header and relying on '.xml'-like extensions.

> As such the next release of the Recess Framework, too, will disable Accept header based content-negotiation by default.

这里的twitter就是直接通过解析扩展来确定类型的。

因为不同的浏览器对于某种类型的文件都有自己特定的accept定义，告诉服务器自己接受什么类型的文件。
如：firefox浏览器对于html文件的accept是：
> text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8

表示

> Dear host,
I want the resource in an HTML or XHTML format. If you cannot serve me this way, I'll take it in an XML instead. If you can't even give it to me in XML, well, I'll take anything you've got!
Love,
Firefox

ie 呢，就发一大堆：

> image/jpeg, application/x-ms-application, image/gif, application/xaml+xml, image/pjpeg, application/x-ms-xbap, application/x-shockwave-flash, application/msword, */*

[具体不同浏览器的文件类型对应的accept形式](https://developer.mozilla.org/en-US/docs/Web/HTTP/Content_negotiation)

## content-type

content-type不管是请求还是响应都应该有。

响应中的content-type很好理解。通过请求中的accept来告知响应内容的类型。

那么请求中的content-type又是什么意思呢？

如果是post方法，那么空格后的那个message-body 里的格式就可以由content-type来传达。比如form。application/x-www-form-urlencoded or multipart/form-data

但如果是get方法呢？为什么需要这个东西？

我发现如果把响应中的content-type写成text/html 刷新时浏览器会渲染html页面。而如果把content-type写成img/jpeg，刷新chrome浏览器的时候会直接下载文件。可能这就是chrome浏览器针对http协议的一些设置。看来了解http协议这种约定俗称的规范，可以让浏览器帮助完成一些事情。

## 区别
content－type和accept有啥区别呢？虽然都规定了mime的类型，
缓存机制：

HTTP定义了3种缓存机制：
l Freshness allows a response to be used without re-checking it on the origin server, and can be controlled by both the server and the client. For example, the Expires response header gives a date when the document becomes stale, and the Cache-Control: max-age directive tells the cache how many seconds the response is fresh for.
l Validation can be used to check whether a cached response is still good after it becomes stale. For example, if the response has a Last-Modified header, a cache can make a conditional request using the If-Modified-Since header to see if it has changed.
l Invalidation is usually a side effect of another request that passes through the cache. For example, if URL associated with a cached response subsequently gets a POST, PUT or DELETE request, the cached response will be invalidated.

也就是说post发送的东西都不会被缓存，get方法发送的会被缓存哦。

## 参考
[面向站长和网站管理员的Web缓存加速指南](http://www.chedong.com/tech/cache_docs.html)
[Hypertext Transfer Protocol -- HTTP/1.1](https://www.w3.org/Protocols/rfc2616/rfc2616.html)
[HTTP协议详解](https://www.zybuluo.com/yangfch3/note/167490)
[输入网址之后发生了什么](https://www.zybuluo.com/yangfch3/note/113028)