---
title: 了解HTTP的请求和回应
tags:
  - 学习日记
  - python
  - http
  - network
excerpt: >-
  # 背景

  现在的很多家用宽带都提供了公网IPv6地址，真的太方便了，对于需要外部访问电脑、NAS和HomeAssistant这样的用户真的是一个大大的福音。我就写了个很简单的小程序（GitHub）来让电脑定时上报IPv6地址并更新到DNspod解析。

  我就...
date: 2024-02-27 21:40:00
categories:
---

\# 背景 现在的很多家用宽带都提供了公网IPv6地址，真的太方便了，对于需要外部访问电脑、NAS和HomeAssistant这样的用户真的是一个大大的福音。我就写了个很简单的小程序（GitHub）来让电脑定时上报IPv6地址并更新到DNspod解析。 我就...
<!-- more -->
\# 背景

现在的很多家用宽带都提供了公网IPv6地址，真的太方便了，对于需要外部访问电脑、NAS和HomeAssistant这样的用户真的是一个大大的福音。我就写了个很简单的小程序（[GitHub](https://github.com/zhousc11/ipv6-ddns)）来让电脑定时上报IPv6地址并更新到DNspod解析。

我就简单了解了下Python的requests库，了解了下发请求这个操作

# 我了解到的

> HTTP请求一般包含哪些东西？

HTTP请求一般是由4个部分组成的，分别是

1.  请求行Request Line 包含了请求方法（比如POST、GET、PUT等），请求的URL和HTTP版本，比如`GET /index.html HTTP/1.1`
    
2.  请求头Headers 这一部分**挺重要的**，包含了请求的元数据，会有很多有用的信息。比如客户端希望接受的内容类型`Accept`头、客户端的类型`User-Agent`头这些。常见的Headers还包含了`Accept-Encoding`用于表明支持的内容编码、`Connection`表明告诉服务器保持连接方便后续继续请求、`Cookies`用于验证客户端身份、`Authorization`用于认证用户等。其他的还可以自定义任何Headers，当然实际使用的时候会很复杂，不止提到的这么多。比如Postman软件中的这些：
    

![2024-02-27T13:33:07.png](https://zhoushicheng.cn/legacy_imgs/3839261963.png)

3.  空行 请求头和请求体中间有一个空行，用来分开他俩。
    
4.  请求体Body 大部分的请求都有请求体，请求体就是要发送给服务器的数据，比如你提交一个账号和密码，请求体就是你的账号密码的数据，你要上传文件，请求体就是你的文件的二进制数据。
    

请求体的格式和内容应该和Headers中的`Content-Type`相匹配。

我的理解中，一个HTTP请求就像是寄快递一样，请求头就相当于快递单上的物品类别、签收方式、保价信息等，请求行就是快递公司的产品名（比如卡航、陆运、空运、目的地），请求体就相当于是包裹里的物品。

> HTTP响应一般包含哪些内容？

HTTP响应和请求一样包含4个部分，分别是`Request Line`, `Header`, `Body`和空行这四个部分组成的。

主要的区别还是在于`Headers`，响应头的Headers就是更多的注重于服务端的内容。主要有以下这几点：

1.  Content-Type 指示响应体的媒体类型（MIME类型），如`text/html`、`application/json`等。这告诉客户端如何解析响应体中的数据。
2.  Content-Length 表示响应体的长度（字节数）。这对于读取和处理响应体非常重要。
3.  Content-Encoding 指示响应体的编码方式，如`gzip`。这是服务器对响应体进行压缩的一种方式，客户端需要按照这个编码解压数据。
4.  Set-Cookie 服务器通过这个头部向客户端发送cookies。客户端浏览器通常会存储这些cookies并在随后的请求中将它们发送回服务器。
5.  Cache-Control 指示响应数据的缓存策略，如`no-cache`、`private`、`max-age=3600`等，这有助于控制浏览器或代理服务器缓存的行为。
6.  Expires 提供一个日期/时间，之后响应被认为是过时的。这与`Cache-Control`头部相辅相成，用于控制缓存的过期时间。
7.  Last-Modified 表示资源最后修改的日期和时间。客户端可以在后续请求中使用`If-Modified-Since`头部询问自该日期以来资源是否有更改。
8.  ETag 资源的实体标签（Entity Tag）。ETag可以用于检查资源的版本，以便实现更高效的缓存策略，避免不必要的数据传输。
9.  Location 用于重定向，指示客户端去访问指定的URL获取资源。这通常在响应状态码为301（永久移动）或302（临时移动）时使用。
10.  Server 描述了处理请求的服务器软件信息。

比如我请求了一下[我的博客](https://zhoushicheng.cn)，返回了这些headers：

![2024-02-27T13:39:16.png](https://zhoushicheng.cn/legacy_imgs/2326693196.png)

包含了很多用于服务器和我们的客户端相互通信协调的东西。

# 使用requests库发送简单的请求

python的requests库真的是一个很强大的库，可以自定义做出任何一个数据包，并以任何方式发送给服务器。我只是简单了解了一下基本用法而已，很菜很菜的

## 安装

```python
pip install requests
```

## 简单使用的例子

```python
import requests

# set user-agent, note that headers is not case sensitive
headers = {'user-agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/122.0.0.0 Safari/537.36'}

# target URL
url = 'https://zhoushicheng.cn'

# send a GET request and get a response object
response = requests.get(url, headers=headers)

# check if request is successful
if response.status_code == 200:  # 200 is an integer, not str
    print(response.text)
else:
    print('Request failed! Statuscode:', response.status_code)
```

这是一个很简单的请求示例，我们可以直接用`requests.get()`来向URL发送请求。其中的知识点包括，响应是一个对象，我们赋给一个变量来获取，这个变量有很多信息可以获取，比如代码中涉及到的`response.text`, `response.status_code`等等。

在`requests.get()`中，我们还可以指定很多选项，比如在headers字典中放入更多headers，关闭SSL验证`verify=False`等等。

## 对于响应的属性的获取

我们发送了一个请求，响应也包含很多信息，我们可以选择性的获取到响应的字段，更加定制化的运行我们的应用程序。

比如这个：

```python
server_header = response.headers.get('Server')
```

就是获取headers中的server。其实还有很多很多可以获取的，比如`response.headers.get('Content-Type')`, `response.headers.get('Content-Length')`等。

获取这些请求可以帮助我们更加自定义我们的应用，例如控制超时时间、控制响应的大小等等，真的很方便的。

## 异常处理

网络连接真的是一个很没有确定性的东西，鬼知道服务器什么时候多久给回复，也不知道网络会不会突然断掉，也不知道服务器崩了没，所以做好异常处理还是挺重要的，我们应该考虑到几种常见的错误并且捕获处理，比如这样：

```python
try:
    response = requests.get(req_url, timeout=10, headers=headers, verify=False)
    response.raise_for_status()  # 如果响应状态码指示错误，将抛出HTTPError异常
    content_type = response.headers.get('content-type', 'unknown')  # 更安全的获取headers
    return response.text, content_type
except requests.exceptions.HTTPError as e:
    print(f"HTTP错误: {e}")
except requests.exceptions.ConnectionError as e:
    print("连接错误")
except requests.exceptions.Timeout as e:
    print("请求超时")
except requests.exceptions.RequestException as e:
    print(f"请求出错: {e}")
return None
```

捕获了常见的错误，使得我们的代码安全性更高，能够处理一些错误，方便我们进行调试，整个程序也不至于直接崩溃。

* * *

我是六六，希望一起加油努力，欢迎评论指正！加油加油加油！

[主页](https://zhoushicheng.cn)