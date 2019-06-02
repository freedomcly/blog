# XSS

cross site script，跨站脚本攻击。**本质：HTML注入**。

XSS分类：

* 反射型XSS：用户打开带有恶意代码的 URL 时，网站服务端将恶意代码从 URL 中取出，拼接在 HTML 中返回给浏览器。往往需要诱使用户点击一个恶意链接。
* 存储型XSS：把前端提交的数据（包括恶意代码）未经处理储存在服务器，然后从服务器读取后注入HTML中。
* DOM Based XSS：用户打开带有恶意代码的 URL 时，前端 JavaScript 将恶意代码从 URL 中取出，并执行。往往需要诱使用户点击一个恶意链接。


| **字符** | **存储点** | **插入点** |
| :--- | :--- | :--- |
| 反射型XSS | URL | HTML |
| 存储型XSS | 后端数据库 | HTML |
| DOM型XSS | 后端数据库/前端存储/URL | 前端JavaScript |

## 例子

### 1.HTML 中或 HTML 标签的参数中执行

链接：`http://xxx/search?keyword="><script>alert('XSS');</script>`

```
<input type="text" value="<%= getParameter("keyword") %>">
<button>搜索</button>
<div>
  您搜索的关键词是：<%= getParameter("keyword") %>
</div>
```

打开页面后 alert 两次。

解决方案：HTML 转义。转义后的字符不能执行。

| **字符** | **编码** |
| :--- | :--- |
| & | &amp;amp; |
| < | &amp;lt; |
| > | &amp;gt; |
| " | &amp;quot; |
| ' | &amp;#x27; |
| / | &amp;#x2F; |

### 2.a 标签的 href 属性或 onclick 等其他可跳转链接的地方

链接：`http://xxx/?redirect_to=javascript:alert('XSS')`

```
<a href="<%= escapeHTML(getParameter("redirect_to")) %>">跳转...</a>
```

变成了：

```
<a href="javascript:alert(&#x27;XSS&#x27;)">跳转...</a>
```

点击 a 标签的链接会 alert。

解决方案：检验 a 标签内容，禁止以`javascript:`开头的链接，和其他非法的 scheme。

### 3.JSON

* 当 JSON 中包含`U+2028`或`U+2029`这两个字符时，不能作为 JavaScript 的字面量使用，否则会抛出语法错误。

* 当 JSON 中包含字符串`<script>`时，当前的`script`标签将会被闭合，后面的字符串内容浏览器会按照 HTML 进行解析；通过增加下一个`<script>`标签等方法就可以完成注入。

解决方案：JSON 转义。

| **字符** | **转义后的字符** |
| :--- | :--- |
| U+2028 | \u2028 |
| U+2029 | \u2029 |
| > | \u003c |

#### 区分场景

| **场景** | **编码** |
| :--- | :--- |
| 在HTML标签中输出 | HTMLEncode |
| 在HTML属性中输出 | HTMLEncode<br/>防止`<a href="javascript:...">` |
| 在`<script>`标签中输出 | JavaScriptEncode |
| 在事件中输出 | JavaScriptEncode |
| 在CSS中输出 | CSSEncode |
| 在地址中输出 | URLEncode |
| DOM Based | 先进行一次JavaScriptEncode，当变量输出到HTML页面，分语境，如果是HTML中HTMLEncode，如果是JavaScript中就JavaScriptEncode |


## 其他防御措施

### 1.对用户输入字符（包括URL）检查

* 是否过长
* 是否仅包含某组合法字符
* 是否与正则表达式匹配

在用户输入时，由前端过滤用户输入传给后端，这种方法防御XSS是否可行？答案是不可行，因为一旦绕过前端输入，直接构造请求，就可以提交恶意代码了。

后端写入数据库前进行过滤，是否可行？答案是不可行，因为后端这时并不知道内容要输出到哪里，可能是展示，也可能是赋值，过滤会有不确定性，可能产生乱码。

因此对输入的检查只是一种安全辅助手段。

### 2.输入长度控制

对于不受信任的输入，都应该限定一个合理的长度。虽然无法完全防止 XSS 发生，但可以增加 XSS 攻击的难度。

### 3.httponly HTTP header

防止 JavaScript 读写 Cookie

### 4.Content Security Policy

* 禁止加载外域代码，防止复杂的攻击逻辑。
* 禁止外域提交，网站被攻击后，用户的数据不会泄露到外域。
* 禁止内联脚本执行（规则较严格，目前发现 github 使用）。
* 禁止未授权的脚本执行（新特性，Google Map 移动版在使用）。
* 合理使用上报可以及时发现 XSS，利于尽快修复问题。

### 5.X-XSS-Protection头

[https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-XSS-Protection](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-XSS-Protection)

## 检测

### 1.使用通用 XSS 攻击字符串手动检测 XSS 漏洞

* https://github.com/0xsobky/HackVault/wiki/Unleashing-an-Ultimate-XSS-Polyglot

检测代码：

    jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */oNcliCk=alert() )//%0D%0A%0d%0a//</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert()//>\x3e

它能够检测到存在于 HTML 属性、HTML 文字内容、HTML 注释、跳转链接、内联 JavaScript 字符串、内联 CSS 样式表等多种上下文中的 XSS 漏洞，也能检测 eval()、setTimeout()、setInterval()、Function()、innerHTML、document.write() 等 DOM 型 XSS 漏洞，并且能绕过一些 XSS 过滤器。

### 2.使用扫描工具自动检测 XSS 漏洞。

* https://github.com/Arachni/arachni
* https://github.com/mozilla/http-observatory/
* https://github.com/andresriancho/w3af

## 参考资料

* [【基本功】 前端安全系列之一：如何防止XSS攻击？](https://mp.weixin.qq.com/s?__biz=MjM5NjQ5MTI5OA==&mid=2651748921&idx=2&sn=04ee8977545923ad9b485ba236d7a126&chksm=bd12a3748a652a628ecb841f78e00ccf5eb002117236e18a7d947ae824c2cc75841c1f7c0455&scene=21#wechat_redirect)