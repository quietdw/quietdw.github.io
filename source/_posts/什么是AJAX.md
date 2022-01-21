---
title: 什么是AJAX
tags:
  - AJAX
---

# 什么是 AJAX

1999 年，微软公司发布 IE 浏览器 5.0 版，第一次引入新功能：允许 JavaScript 脚本向服务器发起 HTTP 请求。这个功能当时并没有引起注意，直到 2004 年 Gmail 发布和 2005 年 Google Map 发布，才引起广泛重视。2005 年 2 月，AJAX 这个词第一次正式提出，它是 Asynchronous JavaScript and XML 的缩写，指的是通过 JavaScript 的异步通信，从服务器获取 XML 文档从中提取数据，再更新当前网页的对应部分，而不用刷新整个网页。后来，AJAX 这个词就成为 JavaScript 脚本发起 HTTP 通信的代名词，也就是说，只要用脚本发起通信，就可以叫做 AJAX 通信。W3C 也在 2006 年发布了它的国际标准。

<!-- more -->

具体来说，AJAX 包括以下几个步骤。

1. 创建 XMLHttpRequest 实例
2. 发出 HTTP 请求
3. 接收服务器传回的数据
4. 更新网页数据

概括起来，就是一句话，**AJAX 通过原生的 XMLHttpRequest 对象发出 HTTP 请求，得到服务器返回的数据后，再进行处理**。现在，服务器返回的都是 JSON 格式的数据，XML 格式已经过时了，但是 AJAX 这个名字已经成了一个通用名词，字面含义已经消失了。

```javascript
function ajax(option) {
  return new Promise(function (resolve, reject) {
    let url = option.url
    let method = option.method
    let body = option.body
    let success = option.success
    let fail = option.success

    var httpRquest = new XMLHttpRequest()
    httpRquest.open(method, url)
    httpRquest.onreadystatechange = function () {
      if (httpRquest.readyState === 4 && httpRquest.status === 200) {
        resolve.call(undefined, httpRquest.responseText)
      } else if (httpRquest.readyState === 4 && httpRquest.status === 404) {
        reject.call(undefined, httpRquest.response)
      }
    }
    httpRquest.send(body)
  })
}
```

# 同源策略

只有 **协议+端口+域名** 一模一样才允许发 AJAX 请求

一模一样一模一样一模一样一模一样一模一样一模一样一模一样一模一样

    - http://baidu.com 可以向 http://www.baidu.com 发 AJAX 请求吗 no
    - http://baidu.com:80 可以向 http://baidu.com:81 发 AJAX 请求吗 no

浏览器必须保证

只有 协议+端口+域名 一模一样才允许发 AJAX 请求

# 跨域

## CORS

CORS ( Cross-Origin Resource Sharing ) 可以告诉浏览器，我俩一家的，别阻止他。

具体来说，就是在后台设置的头信息之中，增加一个`Access-Control-Allow-Origin`

```javascript
var http = require('http')
var fs = require('fs')
var url = require('url')
var port = process.argv[2]

if (!port) {
  console.log('请指定端口号好不啦？\nnode server.js 8888 这样不会吗？')
  process.exit(1)
}

var server = http.createServer(function (request, response) {
  var parsedUrl = url.parse(request.url, true)
  var path = request.url
  var query = ''
  if (path.indexOf('?') >= 0) {
    query = path.substring(path.indexOf('?'))
  }
  var pathNoQuery = parsedUrl.pathname
  var queryObject = parsedUrl.query
  var method = request.method

  /******** 从这里开始看，上面不要看 ************/

  if (path === '/') {
    var string = fs.readFileSync('./index.html', 'utf8')
    var m = fs.readFileSync('./db', 'utf8') //100
    string = string.replace('$$n$$', m)
    response.setHeader('Content-Type', 'text/html', 'charset=utf-8')
    response.write(string)
    response.end()
  } else if (pathNoQuery === '/count' && method.toUpperCase() === 'GET') {
    response.statusCode = 200
    var m = fs.readFileSync('./db', 'utf8')
    m = parseInt(m, 10)
    var n = m + 1
    fs.writeFileSync('./db', n)
    response.setHeader('Content-Type', 'text/json', 'charset=utf-8')
    response.setHeader('Access-Control-Allow-Origin', 'http://127.0.0.1:8008') //最重要的是这一句
    response.write(`
        {
          "count":"${n}"
        }
    `)
    response.end()
  } else {
    response.statusCode = 404
    response.write('网页走丢了')
    response.end()
  }

  /******** 代码结束，下面不要看 ************/
})

server.listen(port)
console.log('127.0.0.1:' + port)
```

## JSONP

JSONP 只能 get，不够安全。[什么是 JSONP >>](https://jiangnana.fun/2018/12/18/%E4%BB%80%E4%B9%88%E6%98%AFJSONP/)

> 引用

- https://wangdoc.com/javascript/bom/xmlhttprequest.html
