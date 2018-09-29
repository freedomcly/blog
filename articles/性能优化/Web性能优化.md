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

![](/assets/timing-overview.png)

浏览器Rendering过程，从DOM树到生成可视化图像，可以用这张图说明：

![](/assets/rendering-performance.png)

也可以参考这篇文章[浏览器输入URL到页面展示的过程](../端到端/浏览器输入URL到页面展示的过程.md)。整个过程的每个环节如果耗时太久，都可以进行优化。

## 优化方法

### 1.连接相关

减少不必要的连接。

* 搭建支持HTTP/2.0或SPDY协议的服务器（多路复用，报头压缩等）
* 启用持久链接（HTTP/1.1中默认开启keep-alive，避免TCP握手和慢启动延迟）
* 避免重定向（301和302，减少DNS解析和TCP连接）
* 避免页面中的失效链接，如img无效链接、空链接（减少请求）
* 避免404
* 减少HTTP请求数量
* 使用HTTP强制缓存和协商缓存
* 简单查询使用GET请求而非POST请求（GET请求只发送头部，POST请求发送头部和正文）
* Gzip
* 减少DNS查找
* DNS预解析（`dns-prefetch`）
* 使用CDN

针对HTTP/1.1和HTTP/2.0的优化不相同。HTTP/2.0不需要合并压缩资源，不需要把资源分布在多个域以求并行加载。

### 2.渲染相关（Rendering）

不用阻塞渲染。

* CSS文件放在head中（由于CSS文件会阻塞渲染，应该在HTML头部就加载好CSS）
* inline CSS
* JS文件放在body底部（由于JS文件会阻塞HTML的解析，对于渲染DOM树并没有帮助，应该在HTML解析和渲染好再加载，因此放在底部）
* JS文件可以使用async和defer标志
* [减少重排重绘](./重排重绘.md)
* 外部的JavaScript
* 避免长时间执行的脚本
* 避免使用table标签（由于table中的内容是表格渲染完成后一次性渲染到页面的，表格内容较多时会渲染较慢）
* 避免使用iframe标签

### 3.资源最小化

如无必要，勿增实体。

* 减少DOM节点数量
* 减少iFrame数量
* 在生产环境使用合并和压缩后的html、css、js、图片文件
* gzip
* 减少cookie体积

### 4.缓存

* HTTP强制缓存：Cache-Control（对应HTTP/1.0的Expires）
* HTTP协商缓存：Etag/If-None-Match和Last-Modified/If-Modified-Since
* DNS缓存

### 5.预加载和懒加载

在适当的请求下做好预加载和懒加载。

* 预加载。在空闲时间把即将使用的资源分成50毫秒的小块进行加载，以确保及时响应。如：用户鼠标移到tab项上就开始请求tab里的内容
* 懒加载。如：图片lazyload，webpack分割代码按需加载

### 6.图片相关

详情：[图像优化](./图像优化.md)

### 7.良好的研究和编码（RD）习惯

* 使用最合适的框架版本（如：使用vue的runtime-only版本）
* 避免使用CSS表达式
* 减少DOM操作

### 8.移动端

网络加载类：
* 首屏直出，避免使用JavaScript加载后请求数据
* 按需加载，非首屏内容滚屏加载，保证首屏内容最小化
* 模块化资源并行下载
* inline首屏必需的CSS和JavaScript
* DNS预解析（`dns-prefetch`）
* 资源预加载
* 合理利用MTU（TCP最大网络传输单元），HTML文件在1kb内，保证在一个RTT内请求完成

缓存类：
* 静态资源离线方案（Service Worker）
* [AMP HTML](https://www.ampproject.org/)

脚本类：
* 使用id选择器
* 合理缓存DOM对象
* 尽量使用事件代理，避免事件直接绑定
* 使用touch事件
* 避免touchmove、scroll连续触发（debounce）
* 使用字符串模板
* 使用ES6+

渲染类：
* viewport固定屏幕
* 使用CSS3动画，开启硬件加速渲染
* 合理使用canvas和requestAnimationFrame
* 不滥用float
* 不滥用web字体

架构协议类：
* 后端渲染数据
* Native View代替DOM

### 9.其他

* 服务器端渲染
* 无限列表DOM回收
* TCP预连接、页面预渲染（出自《Web性能权威指南》）

## 参考资料

* [Google Performance](https://developers.google.com/web/fundamentals/performance/why-performance-matters/)
* [Best Practices for Speeding Up Your Web Site](https://developer.yahoo.com/performance/rules.html?guccounter=1)
* [High Performance Browser Networking](https://hpbn.co/)