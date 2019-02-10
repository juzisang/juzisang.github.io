---
title: 关于CORS，你需要知道的
date: 2018-02-10
categories:
  - Web
tags:
  - Http
  - Web
---

CORS 全称 Cross-origin resource sharing（跨域资源共享）

用来解决浏览器跨域的其中一个方案

使用 Ajax 发起一个请求，如果这个请求的协议、域名、端口只要有一个与当前文档所处的地址不同，就会出现报错。

<!--more-->

CORS 全称 Cross-origin resource sharing（跨域资源共享）

用来解决浏览器跨域的其中一个方案

## 什么时候需要CORS

使用 Ajax 发起一个请求，如果这个请求的协议、域名、端口只要有一个与当前文档所处的地址不同，就会出现报错。

![image](/images/关于CORS，你需要知道的/error1.png)

而为了解决这个问题，就需要用到 CORS

具体可以阅读[浏览器的同源策略](https://developer.mozilla.org/zh-CN/docs/Web/Security/Same-origin_policy)


## 怎么使用CORS

只需要服务器在请求，响应头里增加

```
// 允许制定域名访问，注意协议、域名、端口要写全
Access-Control-Allow-Origin:https://www.juzisang,com

// 允许所有域名访问
Access-Control-Allow-Origin:*
```

## 请求分类
请求分为两种，简单请求、复杂请求，如果我们只设置了上面一个响应头，那么只允许发起简单请求

### 简单请求

- 允许的方法：`GET`、`HEAD`、`POST`
- 允许的请求头
  - `Accept`
  - `Accept-Language`
  - `Content-Language`
  - `Content-Type`
  - [更多...](https://fetch.spec.whatwg.org/#cors-safelisted-request-header)
- `Content-Type`
  - `text/plain`
  - `multipart/form-data`
  - `application/x-www-form-urlencoded`

### 复杂请求

使用了简单请求以外的 `Http` 方法或者请求头，浏览器将会发起一个 `OPTIONS` 预请求。

如果服务器没有返回对应的信息，将会报如下错误

- 添加简单请求以外的请求头

![image](/images/关于CORS，你需要知道的/error2.png)

- 使用简单请求以外的方法 

![image](/images/关于CORS，你需要知道的/error3.png)

## 发起复杂请求
工作主要集中在服务端，需要服务端在响应中返回对应的响应头，允许我们添加这些属性，及使用这些方法

### 允许 Http 方法
```
// 允许指定方法，逗号隔开
"Access-Control-Allow-Methods": "PUT,DELETE,GET,POST"

// 允许所有方法
"Access-Control-Allow-Headers": "*"
```

### 允许自定义请求头
```
// 允许指定请求头，逗号隔开
"Access-Control-Allow-Headers": "XXX,Authorization"
```

### 允许传输 Cookie
```
// 设置为 true 允许
Access-Control-Allow-Credentials:true
```

### OPTIONS 方法间隔时间
如果没有设置这个头，每次请求都会发起 `OPTIONS`
```
// 第二次请求，间隔直接在 1000 秒之内，不在发起 OPTIONS 请求
"Access-Control-Max-Age": 1000
```

### 允许客户端可以读取的响应头
```
// 允许客户端读取 XXX,CHeader
Access-Control-Expose-Headers:"XXX,CHeader"

// 允许所有响应头可被读取
Access-Control-Expose-Headers:"*"
```

## 代码
上面演示实例及报错实例在[这里](https://github.com/JuZiSang/coding-test/blob/master/node/http/index.js)