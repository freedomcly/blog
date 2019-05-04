# 浏览器输入 URL 到页面展示的过程

## 过程

* 浏览器主机名扩展，历史记录扩展
* 浏览器 URL 解析，确定协议名、域名等
* DNS 解析
* [TCP 三次握手和 TLS 握手](./TCP三次握手和TLS握手.md)
* HTTP Request，连同浏览器的 Cookie 和 UserAgent 信息发送请求
* Server 处理
* HTTP Response，根据情况确定返回码 304 或 200
* [浏览器渲染](./浏览器渲染.md)

另外，WebKit 从资源池中查找资源的关键字是 URL。如果两个资源内容完全一样而 URL 不同，会被认为是不同资源。