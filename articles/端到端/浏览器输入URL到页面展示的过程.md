# 浏览器输入URL到页面展示的过程

## 过程

* 浏览器主机名扩展，历史记录扩展
* 浏览器URL解析，确定协议名、域名等
* DNS解析
* [TCP三次握手和TLS握手](./TCP三次握手和TLS握手.md)
* HTTP Request，连同浏览器的Cookie和UserAgent信息发送请求
* Server处理
* HTTP Response，根据情况确定返回码304或200
* [浏览器渲染](./浏览器渲染.md)

WebKit从资源池中查找资源的关键字是URL。如果两个资源内容完全一样而URL不同，会被认为是不同资源。