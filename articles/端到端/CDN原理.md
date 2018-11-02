# CDN原理

Content Delivery Network，即内容分发网络。

![](/assets/cdn.png)

* Origin Server：源站，也就是做 CDN 之前的客户真正的服务器。
* User：访问者，也就是问网站的网民。
* Edge Server：CDN 的服务器，不单指“边缘服务器”。

## 为什么要用CDN

减少传输延迟。

## 调度

* LDNS调度
* HTTP302调度
* HTTP DNS调度

## 参考

* [《CDN 之我见》系列一：原理篇（由来、调度）](https://yq.aliyun.com/articles/577708?spm=a2c4e.11153940.blogcont599253.17.7fd0198egjTukm)
* [《CDN 之我见》系列二：原理篇（缓存、安全）](https://yq.aliyun.com/articles/599253?spm=a2c4e.11153940.blogcont577708.18.584527faRxtSJo)
* [《CDN 之我见》系列三：详解篇（网络优化）](https://yq.aliyun.com/articles/604600?spm=a2c4e.11153940.blogcont599253.29.6be1198e1a1mwJ)