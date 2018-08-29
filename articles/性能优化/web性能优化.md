# Web性能优化

提升web性能有利于提升用户留存率和转化率。

## 第一步：寻找瓶颈

性能优化中最重要的是寻找瓶颈，找出当前最耗时最耗内存的部分，如同找出木桶上最短的木板，把它加长是让木桶承载更多水的关键。

怎么寻找瓶颈呢？Google Dev Tools有两个很好用的tab：Network和Performance。除此之外，还有很多性能测试工具：[https://developers.google.com/web/fundamentals/performance/speed-tools/](https://developers.google.com/web/fundamentals/performance/speed-tools/)

关键指标：
* Response —— 小于100毫秒
* Animation —— 60fpt，也就是每一帧完成时间小于16毫秒
* Idle —— 空闲时间可以预加载，预加载的工作分成耗时50毫秒的多个块，以确保及时响应
* Loading —— 1000毫秒内加载完成

## Loading和Rendering

浏览器Loading过程，从URL到DOM树，可以用这张图说明：

![](/assets/loading-performance.png)

浏览器Rendering过程，从DOM树到生成可视化图像，可以用这张图说明：

![](/assets/rendering-performance.png)

也可以参考这篇文章[浏览器输入URL到页面展示的过程](../端到端/浏览器输入URL到页面展示的过程.md)。整个过程的每个环节如果耗时太久，都可以进行优化。

## 优化方法

### 1.连接相关

减少不必要的链接。

* 搭建支持HTTP/2.0或SPDY协议的服务器（多路复用，报头压缩等）
* 启用持久链接（HTTP/1.1中默认开启keep-alive，避免TCP握手和慢启动延迟）
* 减少重定向（减少DNS解析和TCP连接）
* 避免页面中的失效链接，如img无效链接、空链接（减少请求）
* 避免404
* 使用HTTP强制缓存和协商缓存
* 简单查询使用GET请求而非POST请求

针对HTTP/1.1和HTTP/2.0的优化不相同。HTTP/2.0不需要合并压缩资源，不需要把资源分布在多个域以求并行加载。

### 2.渲染相关（Rendering）

* CSS文件放在head中（由于CSS文件会阻塞渲染，应该在HTML头部就加载好CSS）
* inline CSS
* JS文件放在body底部（由于JS文件会阻塞HTML的解析，对于渲染DOM树并没有帮助，应该在HTML解析和渲染好再加载，因此放在底部）
* JS文件可以使用async和defer标志
* 减少重排重绘（[减少重绘](../端到端/浏览器渲染.md###其他)）

### 3.资源最小化

如无必要，勿增实体。

* 减少HTTP请求
* 减少DOM节点数量
* 减少iFrame数量
* 在生产环境使用合并和压缩后的html、css、js、图片文件
* gzip
* 减少cookie体积

### 4.缓存

* 强制缓存：Cache-Control（对应HTTP/1.0的Expires）
* 协商缓存：Etag/If-None-Match和Last-Modified/If-Modified-Since
* DNS缓存

### 5.预加载和懒加载

在适当的请求下做好预加载和懒加载。

* 预加载。在空闲时间把即将使用的资源分成50毫秒的小块进行加载，以确保及时响应
* 懒加载。如：小程序的分包加载、图片lazyload

### 6.图片相关

* 使用合适的图片格式。颜色数少的图标，可以使用png8或png24；颜色数多的照片，可以使用jpg有损压缩格式
* 进行图片压缩，tinypng
* CSS sprites
* 压缩favicon.ico，使它可缓存
* 避免在HTML中用inline style的方式缩放image

### 7.良好的研究和编码（RD）习惯

* 使用最合适的框架版本（如：使用vue的runtime-only版本）
* 避免使用CSS表达式
* 减少DOM操作

### 8.其他

* 使用CDN
* 服务器端渲染
* 客户端离线化
* 无限列表DOM回收
* DNS预解析、TCP预连接、页面预渲染（出自《Web性能权威指南》）

## 参考资料

* [Google Performance](https://developers.google.com/web/fundamentals/performance/why-performance-matters/)
* [Best Practices for Speeding Up Your Web Site](https://developer.yahoo.com/performance/rules.html?guccounter=1)
* 