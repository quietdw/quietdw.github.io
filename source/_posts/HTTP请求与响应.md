---
title: HTTP请求与响应
date: 2018-10-13 00:00:00
updated: 2018-10-13 00:00:00
tags:
  - HTTP
---

### HTTP 请求

HTTP（HyperText Transfer Protocol）请求信息至少由 3 部分组成

- 请求方法（GET/POST）、URI、协议版本
- 请求头（Request Header）
- 空行（\n）
- 请求正文

<!-- more -->

或

＜ request-line ＞

＜ headers ＞

＜ blank line ＞

＜ request-body ＞

1. 请求方法（GET/POST）、URI、协议版本

   - 根据 HTTP 标准，HTTP 请求可以使用多种请求方法。例如：HTTP1.1 目前支持 7 种请求方法：GET、POST、HEAD、OPTIONS、PUT、DELETE 和 TARCE。
   - 在 Internet 应用中，最常用的方法是 GET 和 POST。

   | 请求方法 | 描述                                                |
   | -------- | --------------------------------------------------- |
   | GET      | 请求获取由 Request-URI 所标识的资源                 |
   | POST     | 在 Request-URI 所标识的资源后附加新的数据           |
   | HEAD     | 请求获取由 Request-URI 所标识的资源的响应消息报头   |
   | OPTIONS  | 请求查询服务器的性能，或查询与资源相关的选项和需求  |
   | PUT      | 请求服务器存储一个资源，并用 Request-URI 作为其标识 |
   | DELETE   | 请求服务器删除由 Request-URI 所标识的资源           |
   | TRACE    | 请求服务器回送收到的请求信息，主要用语测试或诊断    |

2. 请求头(Request Header)

   - 请求头包含许多有关的客户端环境和请求正文的有用信息。例如，请求头可以声明浏览器所用的语言，请求正文的长度等。

   | 请求头                             | 描述                                                                                                                                                                                                                                                                                                                                                                                   |
   | ---------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
   | Content-Type                       | 是返回消息中非常重要的内容，表示后面的文档属于什么 MIME 类型。Content-Type: [type]/[subtype]; parameter。例如最常见的就是 text/html，它的意思是说返回的内容是文本类型，这个文本又是 HTML 格式的。原则上浏览器会根据 Content-Type 来决定如何显示返回的消息体内容                                                                                                                        |
   | Host                               | 指定请求资源的 Intenet 主机和端口号，必须表示请求 url 的原始服务器或网关的位置。HTTP/1.1 请求必须包含主机头域，否则系统会以 400 状态码返回                                                                                                                                                                                                                                             |
   | Accept                             | 浏览器可接受的 MIME 类型                                                                                                                                                                                                                                                                                                                                                               |
   | Accept-Charset                     | 浏览器可接受的字符集                                                                                                                                                                                                                                                                                                                                                                   |
   | Accept-Encoding                    | 浏览器能够进行解码的数据编码方式，比如 gzip。Servlet 能够向支持 gzip 的浏览器返回经 gzip 编码的 HTML 页面。许多情形下这可以减少 5 到 10 倍的下载时间                                                                                                                                                                                                                                   |
   | Accept-Language                    | 浏览器所希望的语言种类，当服务器能够提供一种以上的语言版本时要用到                                                                                                                                                                                                                                                                                                                     |
   | Authorization                      | 授权信息，通常出现在对服务器发送的 WWW-Authenticate 头的应答中                                                                                                                                                                                                                                                                                                                         |
   | Connection                         | 表示是否需要持久连接。如果 Servlet 看到这里的值为“Keep- Alive”，或者看到请求使用的是 HTTP1.1（HTTP 1.1 默认进行持久连接），它就可以利用持久连接的优点，当页面包含多个元素时（例如 Applet，图片），显著地减少下载所需要的时间。要实现这一点，Servlet 需要在应答中发送一个 Content-Length 头，最简单的实现方法是：先把内容写入 ByteArrayOutputStream，然后在正式写出内容之前计算它的大小 |
   | Content-Length                     | 表示请求消息正文的长度                                                                                                                                                                                                                                                                                                                                                                 |
   | Cookie                             | 这是最重要的请求头信息之一                                                                                                                                                                                                                                                                                                                                                             |
   | From                               | 请求发送者的 email 地址，由一些特殊的 Web 客户程序使用，浏览器不会用到它                                                                                                                                                                                                                                                                                                               |
   | Host                               | 初始 URL 中的主机和端口                                                                                                                                                                                                                                                                                                                                                                |
   | If-Modified-Since                  | 只有当所请求的内容在指定的日期之后又经过修改才返回它，否则返回 304“Not Modified”应答                                                                                                                                                                                                                                                                                                   |
   | Pragma                             | 指定“no-cache”值表示服务器必须返回一个刷新后的文档，即使它是代理服务器而且已经有了页面的本地拷贝                                                                                                                                                                                                                                                                                       |
   | Referer                            | 包含一个 URL，用户从该 URL 代表的页面出发访问当前请求的页面                                                                                                                                                                                                                                                                                                                            |
   | User-Agent                         | 浏览器类型，如果 Servlet 返回的内容与浏览器类型有关则该值非常有用                                                                                                                                                                                                                                                                                                                      |
   | UA-Pixels，UA-Color，UA-OS，UA-CPU | 由某些版本的 IE 浏览器所发送的非标准的请求头，表示屏幕大小、颜色深度、操作系统和 CPU 类型                                                                                                                                                                                                                                                                                              |

- 常见的 MIME 类型如下：

  1. text/html ： HTML 格式
  2. text/plain ：纯文本格式
  3. text/xml ： XML 格式
  4. image/gif ：gif 图片格式
  5. image/jpeg ：jpg 图片格式
  6. image/png：png 图片格式
  7. application/xhtml+xml ：XHTML 格式
  8. application/xml ： XML 数据格式
  9. application/atom+xml ：Atom XML 聚合格式
  10. application/json ： JSON 数据格式
  11. application/pdf ：pdf 格式
  12. application/msword ： Word 文档格式
  13. application/octet-stream ： 二进制流数据（如常见的文件下载）
  14. application/x-www-form-urlencoded ： `<form encType=””>`中默认的 encType，form 表单数据被编码为 key/value 格式发送到服务器（表单默认的提交数据的格式）
  15. multipart/form-data ： 需要在表单中进行文件上传时，就需要使用该格式

3. 空行

   - 表示请求头已经结束，接下来的是请求正文

4. 请求正文

   - 请求正文中可以包含客户提交的查询字符串信息，也可以为空

### HTTP 响应

HTTP 应答与 HTTP 请求相似，HTTP 响应也由 3 个部分构成，分别是：

- 状态行
- 响应头(Response Header)
- 空行（\n）
- 响应正文

1. 状态行

   - 由协议版本、数字形式的状态代码、及相应的状态描述，各元素之间以空格分隔，例如`HTTP/1.1 200 OK`
   - 状态码有如下几种：
     - 1xx:指示信息—表示请求已接收，继续处理。
     - 2xx:成功—表示请求已经被成功接收、理解、接受。
     - 3xx:重定向—要完成请求必须进行更进一步的操作。
     - 4xx:客户端错误—请求有语法错误或请求无法实现。
     - 5xx:服务器端错误—服务器未能实现合法的请求。
       > wiki 百科 HTTP 状态码(需翻墙)：[https://zh.wikipedia.org/wiki/HTTP%E7%8A%B6%E6%80%81%E7%A0%81](https://zh.wikipedia.org/wiki/HTTP%E7%8A%B6%E6%80%81%E7%A0%81)

2. 响应头

   - 响应头可能包括：

   Location：

   Location 响应报头域用于重定向接受者到一个新的位置。例如：客户端所请求的页面已不存在原先的位置，为了让客户端重定向到这个页面新的位置，服务 器端可以发回 Location 响应报头后使用重定向语句，让客户端去访问新的域名所对应的服务器上的资源。当我们在 JSP 中使用重定向语句的时候，服务器 端向客户端发回的响应报头中，就会有 Location 响应报头域。

   Server：

   Server 响应报头域包含了服务器用来处理请求的软件信息。它和 User-Agent 请求报头域是相对应的，前者发送服务器端软件的信息，后者发送客户 端软件(浏览器)和操作系统的信息。下面是 Server 响应报头域的一个例子：Server: Apache-Coyote/1.1

   WWW-Authenticate：

   WWW-Authenticate 响应报头域必须被包含在 401(未授权的)响应消息中，这个报头域和前面讲到的 Authorization 请求报头域是 相关的，当客户端收到 401 响应消息，就要决定是否请求服务器对其进行验证。如果要求服务器对其进行验证，就可以发送一个包含了 Authorization 报头域的请求，下面是 WWW-Authenticate 响应报头域的一个例子：WWW-Authenticate: Basic realm="Basic Auth Test!"

   从这个响应报头域，可以知道服务器端对我们所请求的资源采用的是基本验证机制。

   Content-Encoding：

   Content-Encoding 实体报头域被使用作媒体类型的修饰符，它的值指示了已经被应用到实体正文的附加内容编码，因而要获得 Content- Type 报头域中所引用的媒体类型，必须采用相应的解码机制。Content-Encoding 主要用语记录文档的压缩方法，下面是它的一个例子： Content-Encoding: gzip。如果一个实体正文采用了编码方式存储，在使用之前就必须进行解码。

   Content-Language：

   Content-Language 实体报头域描述了资源所用的自然语言。Content-Language 允许用户遵照自身的首选语言来识别和区分实体。 如果这个实体内容仅仅打算提供给丹麦的阅读者，那么可以按照如下的方式设置这个实体报头域：Content-Language: da。

   Content-Length：

   Content-Length 实体报头域用于指明正文的长度，以字节方式存储的十进制数字来表示，也就是一个数字字符占一个字节，用其对应的 ASCII 码存储传输。
   注意的是：这个长度仅仅是表示实体正文的长度，没有包括实体报头的长度。

   Content-Type

   Content-Type 实体报头域用语指明发送给接收者的实体正文的媒体类型。

   Last-Modified

   Last-Modified 实体报头域用于指示资源最后的修改日期及时间。

   Expires

   Expires 实体报头域给出响应过期的日期和时间。通常，代理服务器或浏览器会缓存一些页面。当用户再次访问这些页面时，直接从缓存中加载并显示给用 户，这样缩短了响应的时间，减少服务器的负载。为了让代理服务器或浏览器在一段时间后更新页面，我们可以使用 Expires 实体报头域指定页面过期的时 间。当用户又一次访问页面时，如果 Expires 报头域给出的日期和时间比 Date 普通报头域给出的日期和时间要早(或相同)，那么代理服务器或浏览器就 不会再使用缓存的页面而是从服务器上请求更新的页面。不过要注意，即使页面过期了，也并不意味着服务器上的原始资源在此时间之前或之后发生了改变。

3. 表示分隔

4. 要下载的内容

### 关于 HTTP 请求 GET 和 POST 的区别

- 提交

  - GET 提交，请求的数据会附在 URL 之后（就是把数据放置在 HTTP 协议头＜ request-line ＞中）， 以?分割 URL 和传输数据，多个参数用&连接;例如：`login.action?name=hyddd& password=idontknow&verify=%E4%BD%A0 %E5%A5%BD`。如果数据是英文字母/数字，原样发送，如果是空格，转换为+，如果是中文/其他字符，则直接把字符串用 BASE64 加密，得出如： %E4%BD%A0%E5%A5%BD，其中％XX 中的 XX 为该符号以 16 进制表示的 ASCII。

  - POST 提交：把提交的数据放置在是 HTTP 包的包体＜ request-body ＞中。

  因此，GET 提交的数据会在地址栏中显示出来，而 POST 提交，地址栏不会改变。

- 传输数据的大小：

  首先声明,HTTP 协议没有对传输的数据大小进行限制，HTTP 协议规范也没有对 URL 长度进行限制。 而在实际开发中存在的限制主要有：

  - GET:特定浏览器和服务器对 URL 长度有限制，例如 IE 对 URL 长度的限制是 2083 字节(2K+35)。对于其他浏览器，如 Netscape、FireFox 等，理论上没有长度限制，其限制取决于操作系统的支持。

  因此对于 GET 提交时，传输数据就会受到 URL 长度的限制。

  - POST:由于不是通过 URL 传值，理论上数据不受限。但实际各个 WEB 服务器会规定对 post 提交数据大小进行限制，Apache、IIS6 都有各自的配置。

- 安全性：

  - POST 的安全性要比 GET 的安全性高。比如：通过 GET 提交数据，用户名和密码将明文出现在 URL 上，因为(1)登录页面有可能被浏览器缓存， (2)其他人查看浏览器的历史纪录，那么别人就可以拿到你的账号和密码了。

### 使用开发者工具查看 HTTP 请求内容和响应内容

1. 请求

- 打开 Network
- 地址栏输入网址
- 在 Network 点击，查看 request Headers，点击「view source」,可以看见第一部分和第二部分，第三部分空行看不见
- 如果有请求的第四部分，那么在 FormData 或 Payload 里面可以看到

2. 响应

- 打开 Network
- 地址栏输入网址
- 选中第一个响应查看 Response Headers，点击「view source」，你会看到响应的前两部分
- 查看 Response 或者 Preview，你会看到响应的第 4 部分

### curl 命令的使用

- curl 是一个利用 URL 规则在命令行下工作的文件传输工具，可以说是一款很强大的 http 命令行工具。它支持文件的上传和下载，是综合传输工具
- 语法：`curl [option] [url]`
- 常见参数

```
-A/--user-agent <string>              设置用户代理发送给服务器
-b/--cookie <name=string/file>    cookie字符串或文件读取位置
-c/--cookie-jar <file>                    操作结束后把cookie写入到这个文件中
-C/--continue-at <offset>            断点续转
-D/--dump-header <file>              把header信息写入到该文件中
-e/--referer                                  来源网址
-f/--fail                                          连接失败时不显示http错误
-o/--output                                  把输出写到该文件中
-O/--remote-name                      把输出写到该文件中，保留远程文件的文件名
-r/--range <range>                      检索来自HTTP/1.1或FTP服务器字节范围
-s/--silent                                    静音模式。不输出任何东西
-T/--upload-file <file>                  上传文件
-u/--user <user[:password]>      设置服务器的用户和密码
-w/--write-out [format]                什么输出完成后
-x/--proxy <host[:port]>              在给定的端口上使用HTTP代理
-#/--progress-bar                        进度条显示当前的传送状态
```

- Linux curl 命令详解:[`click here`](https://www.cnblogs.com/duhuo/p/5695256.html)
