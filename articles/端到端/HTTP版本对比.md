# HTTP版本对比

## HTTP版本对比

| **项目** | **HTTP/1.0** | **HTTP/1.1** | **HTTP/2.0** |
| :--- | :--- | :--- | :--- |
| keep-alive | 配置Connection头 | 默认开启 |  |
| 缓存 | Expires/Last-Modified | Cache-Control/Etag |  |
| 性能 |  |  | 多路复用/header压缩 |

## SPDY

HTTP/2.0是SPDY的升级版，都有多路复用、头部压缩等特性。不同点在于：

* 头部压缩算法不同
* SPDY强制使用HTTPS