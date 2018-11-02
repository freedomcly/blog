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

## 工具

* Chrome DevTools(tabs: network / performance)
* Lighthouse——综合性工具，对性能、PWA、可访问性、最佳实践、SEO的综合评估
* PageSpeed Insights
* WebPageTest

## Loading和Rendering

浏览器Loading过程，从URL到DOM树，可以用这张图说明：

![](/assets/timing-overview.png)

浏览器Rendering过程，从DOM树到生成可视化图像，可以用这张图说明：

![](/assets/rendering-performance.png)

也可以参考这篇文章[浏览器输入URL到页面展示的过程](../端到端/浏览器输入URL到页面展示的过程.md)。整个过程的每个环节如果耗时太久，都可以进行优化。

## 优化方法

### 1.连接相关

减少不必要的连接。

* 避免重定向（301和302，减少DNS解析和TCP连接）
* 减少DNS查找
* DNS预解析（`dns-prefetch`）
* 合理利用MTU（TCP最大网络传输单元），HTML文件在1kb内，保证在一个RTT内请求完成（移动端首屏）
* 启用持久链接（HTTP/1.1中默认开启keep-alive，避免TCP握手和慢启动延迟）
* 搭建支持HTTP/2.0或SPDY协议的服务器（多路复用，报头压缩等）
* 避免页面中的失效链接，如img无效链接、空链接（减少请求）
* 避免404
* 减少HTTP请求数量
* 使用HTTP强制缓存和协商缓存
* 简单查询使用GET请求而非POST请求（GET请求只发送头部，POST请求发送头部和正文）
* 使用CDN

针对HTTP/1.1和HTTP/2.0的优化不相同。HTTP/2.0不需要合并资源，不需要把资源分布在多个域以求并行加载。

### 2.渲染相关（Rendering）

不用阻塞渲染。

* CSS文件放在head中（由于CSS文件会阻塞渲染，应该在HTML头部就加载好CSS）
* inline CSS
* 避免使用CSS表达式
* JS文件放在body底部（由于JS文件会阻塞HTML的解析，对于渲染DOM树并没有帮助，应该在HTML解析和渲染好再加载，因此放在底部）
* 外部的JS文件
* JS文件可以使用async和defer标志
* 避免长时间执行的脚本
* [减少重排重绘](./重排重绘.md)
* 避免使用table标签（由于table中的内容是表格渲染完成后一次性渲染到页面的，表格内容较多时会渲染较慢）
* 避免使用iframe标签
* 不滥用float（float布局计算比较耗性能）

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

* 预加载。在空闲时间把即将使用的资源分成50毫秒的小块进行加载，以确保及时响应。如：用户鼠标移到tab项上就开始请求tab里的内容
* 懒加载。如：图片lazyload，webpack分割代码按需加载
* preload和prefetch。对于当前页面很有必要的资源使用preload，对于可能在将来的页面中使用的资源使用prefetch。Chrome会把资源放在HTTP缓存中，如果没有对应的头部不能进行HTTP缓存，则放在内存缓存中

### 6.图片相关

详情：[图像优化](./图像优化.md)

### 7.移动端

* 首屏直出，避免JavaScript加载后请求数据
* inline首屏必需的CSS和JavaScript
* 服务器端渲染
* 按需加载，非首屏内容滚屏加载，保证首屏内容最小化
* 模块化资源并行下载
* 使用touch事件
* 避免touchmove、scroll连续触发（debounce）
* viewport固定屏幕
* 使用CSS3动画，开启硬件加速渲染
* 合理使用canvas和requestAnimationFrame
* Native View代替DOM
* 静态资源离线方案（Service Worker）

### 8.良好的研究和编码（RD）习惯

* 使用最合适的框架版本（如：使用vue的runtime-only版本）
* JS使用id选择器
* 使用事件代理，避免事件直接绑定
* 使用字符串模板
* 使用ES6+

### 9.其他

* 无限列表DOM回收
* [AMP HTML](https://www.ampproject.org/)

## 参考资料

* [Google Performance](https://developers.google.com/web/fundamentals/performance/why-performance-matters/)
* [Best Practices for Speeding Up Your Web Site](https://developer.yahoo.com/performance/rules.html?guccounter=1)
* [High Performance Browser Networking](https://hpbn.co/)