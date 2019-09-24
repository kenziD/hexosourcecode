title: '上传文件multipart/form-data深入解析'
date: 2019-04-02 00:02:20
tags:
---
上传文件是一个前端常见的需求，但是为什么上传文件必须使用content-type:multipart/form-data作为请求头?

<!--more-->


## 起源:multipart/form-data定义源头

multipart/form-data最初由[ 《RFC 1867: Form-based File Upload in HTML》](https://www.ietf.org/rfc/rfc1867.txt)文档提出。

> Since file-upload is a feature that will benefit many applications, this proposes an
extension to HTML to allow information providers to express file upload requests uniformly, and a MIME compatible representation for file upload responses.

1867文档简介中说明文件上传作为一种常见的需求，在目前(1995年)的html中的form表单格式中还不支持，因此提出了一种兼容此需求的mime type。

> The encoding type application/x-www-form-urlencoded is inefficient for sending large quantities of binary data or text containing non-ASCII characters.  Thus, a new media type,multipart/form-data, is proposed as a way of efficiently sending the
values associated with a filled-out form from client to server.

1867文档中也写了为什么要新增一个类型，而不使用旧有的application/x-www-form-urlencoded：因为此类型不适合用于传输大型二进制数据或者包含非ASCII字符的数据。平常我们使用这个类型都是把表单数据使用url编码后传送给后端，二进制文件当然没办法一起编码进去了。所以multipart/form-data就诞生了，专门用于有效的传输文件。

> On the other hand, the
   'multipart' mechanisms are well established, simple to implement on
   both the sending client and receiving server, and as efficient as
   other methods of dealing with multiple combinations of binary data.
  
文档中还解释了为什么要沿用multipart这个机制。multipart机制的定义在[《RFC 1314 - A File Format for the Exchange of Images in the Internet》的7.2 The Multipart Content-Type小节](https://www.w3.org/Protocols/rfc1341/7_2_Multipart.html)


但这个1867文档中对 multipart/form-data 的具体格式并没有写的非常详细，只在第六部分的【Examples】当中给了一个很基本的范例，所以 1998 年又有了一份新的[《RFC 2388：Returning Values from Forms: multipart/form-data 》](https://tools.ietf.org/html/rfc2388)，来阐明不仅在HTTP协议下传输文件、而且使用邮件传输文件时 multipart/form-data 中的各个部分的具体格式<sup><a href="https://moxo.io/blog/2016/11/12/html-form-submit-urlencode-and-multipart-formdata/">[1]</a></sup>。分别对3.Definition of multipart/form-datamultipart/form-data的定义；4.Use of multipart/form-data具体使用格式和方法做了更详细的阐述，其中第四章包括:

##### 4.1封装线的格式

##### 4.2多文件时应使用multipart/mixed

（编者注:实验了一下任何浏览器发送多个文件时都没有实现这个配置[2019-4-5]）但如果发送一封邮件，并使用fiddler抓包，可以看出邮件源码格式还是有严格遵循2388文档规范的。

```
POST https://outlook.office365.com/Microsoft-Server-ActiveSync?jAEECBBv8Usp+29k8LM6azAOecgZBAAAAAALV2luZG93c01haWz/AwEMAQ== HTTP/1.1
Cache-Control: no-cache
Connection: Keep-Alive
Pragma: no-cache
Content-Type: application/vnd.ms-sync
Authorization: ...
X-MS-WL: WindowsPhone/8.0
X-WLAS-Tracing: true
Content-Length: 1498610
Host: outlook.office365.com
Cookie: DefaultAnchorMailbox=yyyy@qq.com

j  EQoid{0:b} P ۻWMIME-Version: 1.0
To: "xxxx@gmail.com" <xxxx@gmail.com>
From: 
Subject: =?utf-8?Q?test=E9=82=AE=E4=BB=B6=E6=A0=BC=E5=BC=8F?=
Date: Fri, 5 Apr 2019 19:43:35 +0800
Importance: normal
X-Priority: 3
Content-Type: multipart/mixed;
	boundary="_8A0F7060-4F31-4015-9BED-E2D8315920AF_"

--_8A0F7060-4F31-4015-9BED-E2D8315920AF_
Content-Type: multipart/alternative;
	boundary="_997001FE-F8E8-4F97-ACC0-BBA66B0710A5_"

--_997001FE-F8E8-4F97-ACC0-BBA66B0710A5_
Content-Transfer-Encoding: base64
Content-Type: text/plain; charset="utf-8"

DQoNCuWPkemAgeiHqiBXaW5kb3dzIDEwIOeJiOmCruS7tuW6lOeUqA0KDQo=

--_997001FE-F8E8-4F97-ACC0-BBA66B0710A5_
Content-Transfer-Encoding: quoted-printable
Content-Type: text/html; charset="utf-8"

文件内容....

--_997001FE-F8E8-4F97-ACC0-BBA66B0710A5_--

--_8A0F7060-4F31-4015-9BED-E2D8315920AF_
Content-Type: image/png; name="upload.png"
Content-Transfer-Encoding: base64
Content-Disposition: attachment; filename="upload.png"

图片内容...
```

## 实例分析

下面举几个栗子:
先使用get方法和post方法，但不写enctype，即以默认的application/x-www-form-urlencoded表格数据格式进行表单请求:

```
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <title></title>
</head>

<body>
	使用get请求,使用application/x-www-form-urlencoded请求头
    <form method="get" action="xxx">
        <label for="name">name</label>
        <input type="text" id="name" name="name">
        <label for="file">file</label>
        <input type="file" id="file" name="file" />
        <input type="submit" value="submit" name="submit">
    </form>
    使用post请求,使用application/x-www-form-urlencoded请求头
    <form method="post" action="xxx">
        <label for="name">name</label>
        <input type="text" id="name" name="name">
        <label for="file">file</label>
        <input type="file" id="file" name="file" />
        <input type="submit" value="submit" name="submit">
    </form>
</body>
</html>
```

windows上使用任何一种服务器(这里使用了iis起在了本机地址1333端口)vs code的go live也可以，并使用了fiddler监听网络请求。如果是在linux可以也可以使用在终端中输入:while :; do clear; nc -l localhost 1333; sleep 2; done的[方式](https://moxo.io/blog/2016/11/12/html-form-submit-urlencode-and-multipart-formdata/)监听端口。
网络请求区别如下:

<figure class="image">
  <img src="https://user-gold-cdn.xitu.io/2019/3/30/169ce841d11bd503?w=1606&h=414&f=jpeg&s=542680" alt="get post对比图">
  <figcaption>图1.1</figcaption>
</figure>

发现post方法和get方法都只是把文件名编码进了url中，文件内容无法得到，这也证实了上述文档中的内容，使用application/x-www-form-urlencoded无法实现文件上传。

若使用enctype='multipart/form-data'，并分别使用post和get方法提交表单则会得到如下结果:

```
    使用get请求,使用multipart/form-data请求头
    <form method="get" action="xxx" enctype="multipart/form-data">
        <label for="name">name</label>
        <input type="text" id="name" name="name">
        <label for="file">file</label>
        <input type="file" id="file" name="file" />
        <input type="submit" value="submit" name="submit">
    </form>
    使用post请求,使用application/x-www-form-urlencoded请求头
    <form method="post" action="xxx" enctype="multipart/form-data">
        <label for="name">name</label>
        <input type="text" id="name" name="name">
        <label for="file">file</label>
        <input type="file" id="file" name="file" />
        <input type="submit" value="submit" name="submit">
    </form>
```

<figure class="image">
  <img src="https://user-gold-cdn.xitu.io/2019/4/2/169de0d68c11cd41?w=2398&h=912&f=jpeg&s=1255308" alt="get post对比图">
  <figcaption>图1.2</figcaption>
</figure>

可以看到在上传文件中使用get方法是无效的，依然只能得到文件名。而post结合multipart/form-data才能真正将文件内容传入请求体。

看到提交文件的格式使用一长串字符作为boundtry封装线对字段进行分割。这也很符合multipart多个部分的语义，包含了多个部分集，每一部分都包含了一个content-desposition头，其值为form-data,以及一个name属性，其值为表单的字段名，文件输入框还可以使用filename参数指定文件名。content-type非必须属性，其值会根据文件类型进行变化，默认值是text/plain。multipart的每一个part上方是边缘，最后一个part的下方添加一个边缘。

## 参考
1. [ RFC 1867: Form-based File Upload in HTML](https://www.ietf.org/rfc/rfc1867.txt)
2. [RFC 2388: Returning Values from Forms:  multipart/form-data](https://tools.ietf.org/html/rfc2388)
3. [为什么上传文件的表单需要设置enctype="multipart/form-data"](https://blog.csdn.net/mazhibinit/article/details/49667511)
4. [表单提交中的 x-www-form-urlencoded 和 multipart/form-data](https://moxo.io/blog/2016/11/12/html-form-submit-urlencode-and-multipart-formdata/)
5. [post Upload上传文件中multipart/form-data 做的那些事](https://www.onmpw.com/tm/xwzj/network_35.html)
6. [阮一峰的网络日志:MIME笔记](http://www.ruanyifeng.com/blog/2008/06/mime.html)
7. [W3C文档规范:17 Forms](https://www.w3.org/TR/html401/interact/forms.html#h-17.13.4.2)
8. [7.2 The Multipart Content-Type(1992)](https://www.w3.org/Protocols/rfc1341/7_2_Multipart.html)