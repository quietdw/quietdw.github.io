---
title: Session、LocalStorage、HTTP缓存
tags:
  - Session
  - LocalStorage
  - Cache-Control
  - HTTP缓存
---

# Session

1. 将 SessionID（随机数）通过 Cookie 发给客户端(Session 可以用 LocalStorage + 查询参数实现)
2. 客户端访问服务器时，服务器读取 SessionID
3. 服务器有一块内存（哈希表）保存了所有 session
4. 通过 SessionID 我们可以得到对应用户的隐私信息，如 id、email
5. 这块内存（哈希表）就是服务器上的所有 session
6. 页面关闭的时候，会清空SessionStorage
7. Session在服务器的默认有效时间是30分钟,可以通过代码控制失效时间

# LocalStorage

1. LocalStorage 跟 HTTP 无关
2. HTTP 不会带上 LocalStorage 的值
3. 只有相同域名的页面才能互相读取 LocalStorage（没有同源那么严格）
4. 每个域名 localStorage 最大存储量为 5Mb 左右（每个浏览器不一样）
5. LocalStorage 永久有效，除非用户清理缓存
6. 常用场景：记录有没有提示过用户（没有用的信息，不能记录密码）

# SessionStorage

1. 同上
2. 同上
3. 同上
4. 同上
5. SessionStorage 在用户关闭页面（会话结束）后就失效

# HTTP缓存

## Cache-Control

1. `Cache-Control: max-age=300`，当用户300s内提交请求时，不会像服务器重新请求
2. 首页不要用缓存

## Expire

1. 和 Cache-Control 类似，优先使用 Cache-Control 
2. `Expires: Wed, 21 Oct 2015 07:28:00 GMT`，在某时刻之前的，都不会像服务器重新请求，但是这个‘时刻’是参照本地时间的，会有风险

## ETag

1. 服务器响应头有Etag，其值为响应内容的MD5
2. 客户端再次访问给定的URL，会返回 If-None-Match 请求头，值还是Etag的值
3. 如果服务器响应内容没更新， 则 If-None-Match 的值和 Etag 的值一致，服务器返回304；如果值不一致，则表示服务器内容有更新，需要重新下载。

## Last-Modified

1. The Last-Modified  是一个响应首部，其中包含源头服务器认定的资源做出修改的日期及时间。 它通常被用作一个验证器来判断接收到的或者存储的资源是否彼此一致。

2. If-Modified-Since是由客户端往服务器发送的请求头，服务器只在所请求的资源在给定的日期时间之后对内容进行过修改的情况下才会将资源返回，状态码为 200  。如果请求的资源从那时起未经修改，那么返回一个不带有消息主体的  304  响应，而在 Last-Modified 首部中会带有上次修改时间。