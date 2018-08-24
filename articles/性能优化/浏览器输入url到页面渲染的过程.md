# 浏览器输入url到页面渲染的过程

## 过程

* 浏览器主机名扩展
* 浏览器历史扩展
* 浏览器url解析
* 查看浏览器内部DNS缓存
* 查看系统DNS缓存，hosts文件
* 查看路由器DNS缓存
* 查看本地DNS服务器
* 查看域名服务器
* 建立DNS缓存
* [TCP三次握手和TLS握手](../端到端/TCP三次握手和TLS握手.md)
* 发起HTTP Request
* Server处理
* 通过CGI连接应用程序
* 返回HTTP Response
* 关闭连接
* 建立DOM Tree
* 建立CSSOM Tree
* 建立Render Tree
* Layout
* Paint
* Composite Layers

## 分阶段解释
### 浏览器
* 浏览器主机名扩展（yahoo => www.yahoo.com）
* 浏览器历史扩展（从历史记录中寻找）
* url解析

问题：

1.什么是合法的url

    scheme:[//[user[:password]@]host[:port]][/path][?query][#fragment]

### DNS解析
* 查看浏览器内部缓存
* 系统缓存
* 路由器缓存
* 本地DNS服务器
* 域名服务器
* 建立DNS缓存


3.四次挥手的详细过程

（一）客户端发生`FIN`

（二）服务器端收到后向客户端发`ACK`

（三）服务器端检查后向客户端发`FIN`

（四）客户端收到后向服务器端发`ACK`


### HTTP请求
* 发起HTTP Request
* Server处理
* 通过CGI连接应用程序
* 返回HTTP Response

问题：

1.HTTP/1.0、HTTP/1.1、HTTP/2.0的区别
* HTTP/2.0性能更好，多路复用，header压缩
* HTTP/1.0缓存头部使用Expires/Last-Modified，HTTP/1.1使用Cache-Control/Etag
* HTTP/1.0中keep-alive需要配置，HTTP/1.1中keep-alive是默认配置好的

2.HTTP和HTTPS的区别
参考[learnHTTP](https://github.com/freedomcly/learnHTTP/blob/master/server/simple-https.js)

3.HTTP协议处于计算机网络的那一层

应用层（1/7）

4.HTTP缓存怎么做，返回码304

参考[learnHTTP](https://github.com/freedomcly/learnHTTP#http-%E7%BC%93%E5%AD%98)

5.同源策略和跨域问题
参考[同源策略和跨域问题](同源策略和跨域问题.md)

### 页面渲染
* 构建DOM Tree（parse HTML）
* 构建Render Tree（recalculate styles, combine html and CSS）
* Layout（计算元素占用的视觉大小、位置）
* Paint
* Composite Layers

问题：

1.DOM Tree和Render Tree的不同

Render Tree是HTML和CSS合并后的渲染树。比起DOM Tree，Render Tree没有head标签和script标签等，没有display: none的节点，增加了伪元素。

2.script标签引入的三种方式怎么影响页面渲染

[http://www.growingwiththeweb.com/2014/02/async-vs-defer-attributes.html](http://www.growingwiththeweb.com/2014/02/async-vs-defer-attributes.html)

3.什么是重排重绘

重排reflow也就是layout，重绘repaint。
[引起layout、paint、composite的CSS属性](https://csstriggers.com/)

4.DOMContentLoaded事件和load事件分别什么时候触发

DOMContentLoaded在DOM构建完成时触发，load在所有依赖资源都加载完成时触发。

## 参考资料

[Google Render Performance](https://developers.google.com/web/fundamentals/performance/rendering/)