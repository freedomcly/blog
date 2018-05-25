# 浏览器输入url到页面渲染的全过程

## 浏览器
* 输入提示
* url解析

这部分可以提问：
1. 什么是合法的url

    scheme:[//[user[:password]@]host[:port]][/path][?query][#fragment]

## DNS解析
* 查看浏览器内部缓存
* 系统缓存
* 路由器缓存
* 本地DNS服务器
* 域名服务器

## TCP连接
* 三次握手

这部分可以提问：
1. 为什么需要三次握手
三次握手为了证明客户端和服务器端都可以收发信息
2. TCP/IP协议处于计算机网络的哪一层
传输层（4/7）

## HTTP请求
* 发起http request
* server处理（查询数据库）
* 返回http response

这部分可以提问：
1. HTTP/1.0、HTTP/1.1、HTTP/2.0的区别
// TODO
2. HTTP和HTTPS的区别
// TODO
3. HTTP协议处于计算机网络的那一层
应用层（1/7）
4. HTTP缓存怎么做
// TODO
5. 返回码301和302的区
// TODO
6. 同源策略和跨域问题
// TODO

## 页面渲染
* 构建DOM Tree（parse HTML）
* 构建Render Tree（recalculate styles, combine html and CSS）
* Layout（计算元素占用的视觉大小、位置）
* Paint
* Composite Layers

这部分可以提问：
1. DOM Tree和Render Tree的不同
2. script标签引入的三种方式怎么印象页面渲染
3. 什么是重排重绘

## 参考资料

* [Google Render Performance](https://developers.google.com/web/fundamentals/performance/rendering/)
* [从浏览器输入一个 url 到页面渲染，涉及的知识点及优化点](https://github.com/sunyongjian/blog/issues/34)