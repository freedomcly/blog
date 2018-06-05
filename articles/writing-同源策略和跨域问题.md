# 同源策略和跨域问题

## 同源策略

[same origin policy](https://en.wikipedia.org/wiki/Same-origin_policy)

同源策略是浏览器的安全策略。同源是指协议、域名、端口均相同。

## 同源共享

### 1.Cookie

cookie默认情况下遵循同源策略。但有两种方式可以同源共享：
* 客户端配置`document.domain`
* 服务器端通过HTTP Response头部配置域名和路径`Set-Cookie: key=value; domain=.example.com; path=/`（二级域名、三级域名都可以共享cookie；特定的path可以共享cookie）

### 2.LocalStorage

### 3.SessionStorage

## 跨域问题

CORS
JSONP
websocket

详情见[learnHTTP](https://github.com/freedomcly/learnHTTP/blob/master/server/cross-domain.js)