# 离线与存储

# 一、离线

离线检测：

* `navigator.onLine`属性
* `online`事件，`window.ononline`
* `offline`事件，`window.onoffline`

# 二、存储

### Cookie

1.Cookie构成：
* 名称
* 值
* 域——用于同源共享
* 路径——用于同源共享
* 失效时间
* 安全标志——只有SSL连接时才发送

2.Cookie和Set-Cookie的区别：Set-Cookie作为响应头会直接在客户端设置cookie，以后的请求会带上Cookie头，而Cookie头只是做信息传递。

3.JavaScript中操作cookie的接口`document.cookie`的行为：

* 读：返回`; `分割的字符串
* 写：不覆盖，在末端添加

由于cookie原生接口比较难用，可以使用操作cookie的工具函数。

4.httponly

防止JavaScript操作cookie，防止cookie劫持。在cookie中不应该存储敏感信息。

5.分为Session Cookie和持久型Cookie

* Session Cookie。不设置过期时间，生命周期为会话期间。保存在内存。
* Third-party Cookie。设置过期时间。保存在硬盘。跨域加载时，有的浏览器会拦截，IE8、Safari。

### Cookie、LocalStorage、SessionStorage对比

| **项目** | **Cookie** | **LocalStorage** | **SessionStorage** |
| :--- | :--- | :--- | :--- |
| 生命周期 | 服务器端可设置过期时间 | 除非被清除，否则永久有效 | 仅在当前会话有效，关闭页面或浏览器后被清除 |
| 大小 | 4KB，50个或无限制，单个域名下，浏览器之间有差异 | 5MB左右，单个域名下，浏览器之间有差异 | 5MB左右，单个域名下 |
| 与服务器通信 | 每次都会携带在HTTP头部 | 仅在客户端保存，不与服务器通信 | 仅在客户端保存，不与服务器通信 |
| 同源限制 | 默认遵循同源策略，可以配置为二级域名共享或特定路径共享 | 默认遵循同源策略，可以配置为iFrame之间共享 | 不同页面间不能共享 |
| 易用性 | 原生的Cookie接口不友好 | 原生接口可以接受 | 原生接口可以接受 |
| 目的 | 为无状态的HTTP协议添加状态管理 | 客户端本地存储 | 客户端会话存储 |

## 参考资料

[详说 Cookie, LocalStorage 与 SessionStorage](http://jerryzou.com/posts/cookie-and-web-storage/)