layout: http
title: 4xx
date: 2016-05-30 10:43:17
tags:
---
这是我阅读http协议4xx状态码的笔记

4xx开头的表示这是客户端出现了错误。

除非请求是head方法，否则服务端都应该在请求体中描述出错的情况，永久错误还是临时性错误。

### 400 Bad Request

表示请求语法错误，而且这个请求也不应该被重复提交。


### 401

### 401 vs 403

401 和 403的区别是，401可能是你认证不通过，也有可能是你压根没有认证那个header，所以你可以修改一下认证重新提交相同的请求，403则不管你有没有认证，即便是认证通过了，但你依然没有权限访问这个uri.

![403 Forbidden vs 401 Unauthorized HTTP responses](http://stackoverflow.com/questions/3297048/403-forbidden-vs-401-unauthorized-http-responses)