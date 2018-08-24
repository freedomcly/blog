# HTTP版本对比

| **项目** | **HTTP/1.0** | **HTTP/1.1** | **HTTP/2.0** |
| :--- | :--- | :--- | :--- |
| keep-alive | 配置Connection头 | 默认开启 |  |
| 缓存 | Expires/Last-Modified | Cache-Control/Etag |  |
| 性能 |  |  | 多路复用/header压缩 |