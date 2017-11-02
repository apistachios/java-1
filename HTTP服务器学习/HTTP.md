# HTTP
HTTP: 超文本传输协议(HyperText Transfer Protocol)是互联网上应用最为广泛的一种网络协议。

## 协议的组成和过程

当在浏览器中输入一个网址后敲回车,浏览器会将你的请求封装成一个http请求的格式发送给服务器,服务器收到请求以后会组织响应数据封装成一个http响应,返回给客户端

- http协议由http请求和http响应组成     
- 没有请求就没有响应


- http请求
  - 请求行: 请求方式 请求的路径(请求的数据) http的版本号
  - 请求头: 主机名 连接方式 客户端浏览器的内核 接受数据类型 压缩格式 语言
  - 请求体: 如果是post请求,请求的数据在此
- http响应
  - 响应行: http版本号 状态码(200,404)
  - 响应头: 文本类型(contenType) cookie last-modified 数据长度 date
  - 响应体: 当前页面要显示的内容

- get请求
```java
//请求行:请求的方法GET,请求的路径带提交的信息HTTP的版本
GET /BigData/a.html?username=213123&passw
ord=3244234 HTTP/1.1
//本机的地址
Host: localhost:8080
//连接的方式: 长连接
Connection: keep-alive
//则是告诉服务器,自己支持这种操作,也就是我能读懂你服务器发过来的上面这条信息,并且在以后发请求的时候不用http而用https
Upgrade-Insecure-Requests: 1
//客户端浏览器内核和响相应的操作系统的相关信息
User-Agent: Mozilla/5.0 (Windows NT 10.0;
WOW64) AppleWebKit/537.36 (KHTML, like Ge
cko) Chrome/58.0.3029.110 Safari/537.36
//告诉服务器客户端浏览器能够接收哪种类型的数据
Accept: text/html,application/xhtml+xml,a
pplication/xml;q=0.9,image/webp,*/ *;q=0.8
//浏览器通知服务器,当前请求来自何处。如果是直接访问,则不会有这个头。常用于:防盗链
Referer: http://localhost:8080/BigData/a.html
//浏览器通知服务器,浏览器支持的数据压缩格式
Accept-Encoding: gzip, deflate, sdch, br
//浏览器通知服务器,浏览器支持的语言
Accept-Language: zh-CN,zh;q=0.8
```
- post请求
```java
POST /BigData/a.html HTTP/1.1
Host: localhost:8080
Connection: keep-alive
//内容的长度
Content-Length: 29
Cache-Control: max-age=0
Origin: http://localhost:8080
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0;
WOW64) AppleWebKit/537.36 (KHTML, like Ge
cko) Chrome/58.0.3029.110 Safari/537.36
//如果使用的是post请求就会有这个头,表示请求体中的数据使用的是url编码
Content-Type: application/x-www-form-urle
ncoded
Accept: text/html,application/xhtml+xml,a
pplication/xml;q=0.9,image/webp,*/ *;q=0.8
Referer: http://localhost:8080/BigData/a.html
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.8
//会有一个空行,下面的内容是请求体的内容
username=12123&password=34324
```
- Http响应
```html
//响应行 http协议
状态码
HTTP/1.1 200
//响应头,表明服务器是否支持指定范围请求及哪种类型的分段请求
Accept-Ranges: bytes
//被请求变量的实体值”,ETag是一个可以与Web资源关联的记号
ETag: W/"373-1499776861532"
//上次修改的时间
Last-Modified: Tue, 11 Jul 2017 12:41:01 GMT
//内容类型
Content-Type: text/html
//响应正文长度
Content-Length: 373
Date: Tue, 11 Jul 2017 12:41:10 GMT
//空行,响应体
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Insert title here</title>
</head>
<body>
    <h1>好好学习,天天向上</h1>
</body>
</html>
```

## 常用响应状态码

|常用状态码| 意义|
|:-:|:-:|
|200 |请求成功|
|302 |重定向|
|304 |读取本地缓存文件|
|404 |请求的页面不存在|
|500 |服务端程序错误|
