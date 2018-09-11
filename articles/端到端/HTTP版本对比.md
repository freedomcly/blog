# HTTP版本对比

## HTTP版本对比

| **项目** | **HTTP/1.0** | **HTTP/1.1** | **HTTP/2.0** |
| :--- | :--- | :--- | :--- |
| 长连接 | 配置`Connection: keep-alive` | 默认开启`keep-alive` | 基于传输层TCP连接的多路复用 |
| 缓存 | Expires/Last-Modified | Cache-Control/Etag |  |
| 并发 | 比HTTP/1.1多（由于HTTP/1.1默认持久连接，因此不支持过多的并发） | 2-8，多数是6个连接 | 多个 |
| 压缩 | | | header压缩 |
| 传输格式 | 默认文本格式 | 默认文本格式 | 二进制 |
| 协议拓展切换 | | 例如切换websocket：`Connection: Upgrade; Upgrade: websocket` | |
| 服务器端推送 | | | 支持 |
| 传输优先级 | | | 支持 |

## SPDY

HTTP/2.0是SPDY的升级版，都有多路复用、头部压缩等特性。不同点在于：

* 头部压缩算法不同，HTTP/2.0使用HPACK算法
* SPDY强制使用HTTPS