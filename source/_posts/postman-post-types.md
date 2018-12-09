---
title: postman 中的 form-data、x-www-form-urlencoded、raw、binary
date: 2018-05-27 01:46:38
tags: [JavaScript, encode, post]
---

哇，我发现自己的人生就是一个不断重复打自己脸的过程。

![](http://qiniu1.letow.top/luoyonghao-slap.gif)

前几天刚刚 [吐槽 postman 不好用](http://blog.letow.top/2018/05/18/use-python-test-post/)，今天马上发现了自己的错误。

以下内容转载自：[postman 中 form-data、x-www-form-urlencoded、raw、binary的区别](https://blog.csdn.net/ye1992/article/details/49998511)。

<!--more-->

**1、form-data **

就是 http 请求中的 **multipart/form-data**，它会将表单的数据处理为一条消息，以标签为单元，用分隔符分开。既可以上传键值对，也可以上传文件。

当上传的字段是文件时，会有Content-Type来表名文件类型；content-disposition，用来说明字段的一些信息。

由于有 boundary 隔离，所以 multipart/form-data 既可以上传文件，也可以上传键值对，它采用了键值对的方式，所以可以上传多个文件。

![img](https://img-blog.csdn.net/20151118130933756?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

![img](https://img-blog.csdn.net/20151118130954483?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

​                 

**2、x-www-form-urlencoded：**

就是 **application/x-www-from-urlencoded**，会将表单内的数据转换为键值对，比如 `name=java&age = 23`

![img](https://img-blog.csdn.net/20151118131219584?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

![img](https://img-blog.csdn.net/20151118131234865?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

**3、raw**

可以上传任意格式的文本，可以上传 text、json、xml、html 等

![img](https://img-blog.csdn.net/20151118131504612?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

![img](https://img-blog.csdn.net/20151118131523976?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

**4、binary**

相当于 **Content-Type:application/octet-stream**，从字面意思得知，只可以上传二进制数据，通常用来上传文件，由于没有键值，所以，一次只能上传一个文件。



**multipart/form-data 与 x-www-form-urlencoded 区别**

multipart/form-data：既可以上传文件等二进制数据，也可以上传表单键值对，只是最后会转化为一条信息；

x-www-form-urlencoded：只能上传键值对，并且键值对都是间隔分开的。