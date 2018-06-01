# Web性能优化

提升web性能有利于提升用户留存率和转化率。性能优化的方法很多，可以分为Loading优化和Rendering优化。

## 第一步：寻找瓶颈

性能优化中最重要的是寻找瓶颈，找出当前最耗时最耗内存的部分，如同找出木桶上最短的木板，把它加长是让木桶承载更多水的关键。

怎么寻找瓶颈呢？Google Dev Tools有两个很好用的tab：Network和Performance。除此之外，还有很多性能测试工具：[https://developers.google.com/web/fundamentals/performance/speed-tools/](https://developers.google.com/web/fundamentals/performance/speed-tools/)

关键指标：
* Response —— 小于100毫秒
* Animation —— 60fpt，也就是每一帧完成时间小于16毫秒
* Idle —— 空闲时间可以预加载，预加载的工作分成耗时50毫秒的多个块，以确保及时响应
* Loading —— 1000毫秒内加载完成

## Loading和Rendering

浏览器Loading过程可以用这张图说明：

![](/assets/loading-performance.png)

浏览器Rendering过程可以用这张图说明：

![](/assets/rendering-performance.png)

也可以参考这篇文章[浏览器输入url到页面渲染的过程](浏览器输入url到页面渲染的过程.md)。整个过程的每个环节如果耗时太久，都可以进行优化。

## 优化方法

### 1.资源最小化

如无必要，勿增实体。

* 减少DOM节点数量
* 减少iFrame数量
* 在生产环境使用合并和压缩后的html、css和js文件
* gzip
* 减少cookie体积

### 2.防止渲染(Rendering)和解析(Parsing)的阻塞

* CSS文件放在head中（由于CSS文件会阻塞DOM树的渲染，应该在HTML头部就加载好CSS）
* JS文件放在body底部（由于JS文件会阻塞HTML的解析，对于渲染DOM树并没有帮助，应该在HTML解析和渲染好再加载，因此放在底部）

### 2.缓存

在适当的情况下做好缓存。


## 参考资料

* [Google Performance](https://developers.google.com/web/fundamentals/performance/why-performance-matters/)
* [Best Practices for Speeding Up Your Web Site](https://developer.yahoo.com/performance/rules.html?guccounter=1)