---
title: 什么是JSONP
tags:
  - JSONP
---

### 什么是 JSONP

请求方：浏览器（前端）

响应方：服务器（后端）

<!-- more -->

1. 浏览器动态创建`<script>`标签， src 指向响应方，并插入到`<body>`中，同时传一个查询参数（ url?callback=funcName ），查询值默认为 callback；
2. 响应方根据查询的参数，构造形如`xxx.call(undefined,'数据')`的内容，并响应给浏览器；
3. 浏览器接收到响应，会自动解析请求到`script`里的数据，执行`xxx.call(undefined,'数据')`，就得到想要的数据；
4. 请求完成或请求失败，都会删除生成`<script>`标签和构建的`xxx`全局函数。

### 两个约定：

1. 默认查询参数的值`callback=funcName`；
2. funcName 使用随机数，例如 func123456。

### JSONP demo

- html 页面

```html
<!DOCTYPE html>
<html lang="zh-Hans">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>JSONP-demo</title>
  </head>

  <body>
    <p>你还有<span id="money">$$money$$</span>块钱</p>
    <button id="btn">扣1块钱</button>
    <script>
      btn.addEventListener('click', function () {
        func = 'func' + parseInt(Math.random() * 10000, 10)

        window[func] = function (respones) {
          if (respones.success) {
            money.innerText = respones.money
          }
        }
        var script = document.createElement('script')
        script.src = 'http://jack.com:8888/pay?callback=' + func
        document.body.appendChild(script)
        script.onload = function (e) {
          e.currentTarget.remove()
          delete window[func]
        }
        script.onerror = function (e) {
          e.currentTarget.remove()
          alert('failed')
          delete window[func]
        }
      })
    </script>
  </body>
</html>
```

- server.js

```

var http = require('http')
var fs = require('fs')
var url = require('url')
var port = process.argv[2]

if(!port){
  console.log('请指定端口号好不啦？\nnode server.js 8888 这样不会吗？')
  process.exit(1)
}

var server = http.createServer(function(request, response){
  var parsedUrl = url.parse(request.url, true)
  var path = request.url
  var query = ''
  if(path.indexOf('?') >= 0){ query = path.substring(path.indexOf('?')) }
  var pathNoQuery = parsedUrl.pathname
  var queryObject = parsedUrl.query
  var method = request.method

  /******** 从这里开始看，上面不要看 ************/

  if (path === '/') {
    var string = fs.readFileSync('./index.html','utf8')
    var account = fs.readFileSync('./db','utf8') //100
    string=string.replace('$$money$$',account)
    response.setHeader('Content-Type','text/html','charset=utf-8')
    response.write(string)
    response.end()
  }else if(pathNoQuery === '/pay' && method.toUpperCase() ==='GET' ){
    var account = fs.readFileSync('./db','utf8')
    var newaccount = account -1
    fs.writeFileSync('./db',newaccount)
    response.setHeader('Content-Type','application/javascript')
    response.write(`
      ${queryObject.callback}.call(undefined,{
        'success':true,
        'money':`+newaccount+ `
      })
    `)
    response.end()
  }else{
    response.statusCode = 404
    response.write('网页走丢了')
    response.end()
  }

  /******** 代码结束，下面不要看 ************/

})

server.listen(port)
console.log('127.0.0.1:'+port)

```
