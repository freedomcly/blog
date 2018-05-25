# 浏览器输入url到页面渲染的全过程

## 浏览器
* 输入提示
* url解析

## DNS解析
* 查看浏览器内部缓存
* 系统缓存
* 路由器缓存
* 本地DNS服务器
* 域名服务器

## TCP连接
* 三次握手

## HTTP请求
* 发起http request
* server处理（查询数据库）
* 返回http response

## 页面渲染
* 构建DOM Tree（parse HTML）
* 构建Render Tree（recalculate styles, combine html and CSS）
* Layout（计算元素占用的视觉大小、位置）
* Paint
* Composite Layers


## 参考资料

* [Google Render Performance](https://developers.google.com/web/fundamentals/performance/rendering/)
* [从浏览器输入一个 url 到页面渲染，涉及的知识点及优化点](https://github.com/sunyongjian/blog/issues/34)