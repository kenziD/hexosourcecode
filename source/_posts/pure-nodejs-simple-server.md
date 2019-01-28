ltitle: pure-nodejs-simple-server
date: 2016-05-22 16:02:30
tags: nodeJs,protocal
---
用nodejs的http模块和fs模块。搭建一个简单的服务器。路由规则用正则。

## 创建一个服务端

node可以发送请求，也可以监听某个端口，响应http请求。

创建服务端,并监听2016端口。

处理get请求:get.js

```
// 引用http模块
var http = require("http");
 
// 引用url模块
var url = require("url");
 
// 引用querystring模块
var querystring = require("querystring");
 
http.createServer(function (request, response) {
	var objectUrl = url.parse(request.url);

	var objectQuery = querystring.parse(objectUrl.query);
 
	response.writeHead(200, {
		"content-type": "text/html"
	});
 
	// 输出url的各项参数
	response.write("<h1>objectUrl</h1>");
	for (var i in objectUrl) {
		if (typeof (objectUrl[i]) != "function") 
			response.write(i + "=>" + objectUrl[i] + "<br>");
	}
 
	// 输出url中的query的各项参数
	response.write("<h1>objectQuery</h1>");
	for (var i in objectQuery) {
		response.write(i + "=>" + objectQuery[i] + "<br>");
	}
 
	response.end();
}).listen(2016);
```
在本文件的目录下，运行node get.js

然后浏览器输入localhost:2016回车。

request.url表示请求的url。如："/",如果你输入form。则会返回"/form"。

比如你输了这样一串url:

```
http://localhost:2014/form?name=kenzi&age=20&sex=female

```

url.parse这个模块会对url进行解析：

```
Url {
protocol: null,
slashes: null,
auth: null,
host: null,
port: null,
hostname: null,
hash: null,
search: '?name=kenzi&age=20&sex=female',
query: 'name=kenzi&age=20&sex=female',
pathname: '/form',
path: '/form?name=kenzi&age=20&sex=female',
href: '/form?name=kenzi&age=20&sex=female' 
}
```

[url模块的使用方法](http://itbilu.com/nodejs/core/NJGRdjgU.html)

## 参考

[Accessing the HTTP message body (e.g. POST data) in node.js](http://blog.frankgrimm.net/2010/11/howto-access-http-message-body-post-data-in-node-js/)

[http://www.dotnetcurry.com/nodejs/1144/nodejs-html-static-pages-website](http://www.dotnetcurry.com/nodejs/1144/nodejs-html-static-pages-website)

[https://github.com/dotnetcurry/node.js-html-static-content/blob/master/server.js](https://github.com/dotnetcurry/node.js-html-static-content/blob/master/server.js)

[https://cnodejs.org/topic/51738afd6d38277306ef98ad](https://cnodejs.org/topic/51738afd6d38277306ef98ad)

[mongoose quik start](http://mongoosejs.com/docs/index.html)
## 题外话

因为刚学http协议，很想自己搭建一个服务器实践一下。比如实现一个简单的点赞功能，登录功能等。而且一直想学习node所以选了node作为后端语言。如果以后有机会，也许会用其他语言试一试。网上搜索的很多例子都是nodejs＋express框架。使用express框架可能会方便很多。不过我认为还是应该从基层写起，这样才会知道为啥要使用某个框架，框架帮你处理了什么。