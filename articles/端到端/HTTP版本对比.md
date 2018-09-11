# HTTP版本对比

## HTTP版本对比

| **项目** | **HTTP/1.0** | **HTTP/1.1** | **HTTP/2.0** |
| :--- | :--- | :--- | :--- |
| 长连接keep-alive | 配置Connection头 | 默认开启 |  |
| 缓存 | Expires/Last-Modified | Cache-Control/Etag |  |
| 并发 | 比HTTP/1.1多（由于HTTP/1.1默认持久连接，因此不支持过多的并发） | 2-8，多数是6个连接 | 多个 |
| 性能 |  |  | 多路复用/header压缩 |
| 协议拓展切换 | | 例如切换websocket：`Connection: Upgrade; Upgrade: websocket` |

## SPDY

HTTP/2.0是SPDY的升级版，都有多路复用、头部压缩等特性。不同点在于：

* 头部压缩算法不同
* SPDY强制使用HTTPS