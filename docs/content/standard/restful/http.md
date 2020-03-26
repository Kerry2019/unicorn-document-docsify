对于`HTTP协议`,我们既熟悉，又陌生。熟悉，是因为我们每天开发API服务，都是基于HTTP协议。陌生，是因为我们大多数人在API设计上，都没有完全考虑到HTTP协议的规范。

!>HTTP是基于客户端/服务端（C/S）的架构模型，通过一个可靠的链接来交换信息，是一个无状态的请求/响应协议。HTTP使用统一资源标识符（Uniform Resource Identifiers, URI）来传输数据和建立连接。

一个基于HTTP协议消息，由如下部分组成：

1. HTTP请求方法：HTTP1.0只有GET、POST、HEAD，HTTP1.1之后增加OPTIONS、PUT、PATCH、DELETE、CONNECT等。
2. HTTP响应头：提供了关于请求，响应或者其他的发送实体的信息。
3. HTTP状态码：HTTP状态码由`三个十进制数字`组成，第一个十进制数字定义了状态码的类型，后两个数字没有分类的作用。`1xx`（信息，服务器收到请求，需要请求者继续执行操作）、`2xx`（成功，操作被成功接收并处理）、`3xx`（重定向，需要进一步的操作以完成请求）、`4xx`（客户端错误，请求包含语法错误或无法完成请求）、`5xx`（服务器错误，服务器在处理请求的过程中发生了错误）。
4. content-type：告诉客户端实际返回的内容的内容类型。



我们来看一段HTTP GET请求和响应的报文：

#### 请求报文

```http
GET /users HTTP/1.1
uid: Kerry
User-Agent: PostmanRuntime/7.24.0
Accept: */*
Cache-Control: no-cache
Postman-Token: 4f23c891-9279-4e89-85c9-329b7c47262a
Host: localhost:8002
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
```



#### 响应报文

```http
HTTP/1.1 200 OK
Content-Type: application/json
Transfer-Encoding: chunked
Date: Thu, 26 Mar 2020 07:46:29 GMT
Keep-Alive: timeout=60
Connection: keep-alive
[{"id":41,"username":"Kerry","chinese_name":"吴晨瑞0329","email":"kerry.wu@definesys.com","birthday":"1995-08-18","department_id":1,"object_version":4,"created_by":"Kerry","created_date":"2020.03.08 18:15:14","last_updated_by":"kerry0325","last_updated_date":"2020.03.26 14:38:57","department_name":null}]
```





关于HTTP协议的详细介绍，建议查看[菜鸟教程](https://www.runoob.com/http/http-tutorial.html)。