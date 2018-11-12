# XSS

cross site script，跨站脚本攻击。**本质：HTML注入**。

XSS分类：

* 反射型XSS：把用户的输入（URL参数等）注入HTML中，往往需要诱使用户点击一个恶意链接
* 存储型XSS：把前端提交的数据（包括恶意代码）未经处理储存在服务器，然后从服务器读取后注入HTML中
* DOM Based XSS：通过修改DOM节点形成的XSS。严格来说也是反射型XSS

| **字符** | **存储点** | **插入点** |
| :--- | :--- | :--- |
| 反射型XSS | URL | HTML |
| 存储型XSS | 后端数据库 | HTML |
| DOM型XSS | 后端数据库/前端存储/URL | 前端JavaScript |

## 危险性

* Cookie劫持
* 模拟GET、POST请求

## 防御

由于XSS的本质是HTML注入，那么最好的防御就是防止HTML注入。

### 1.对输出到浏览器的字符检查和编码

针对不同场景的XSS，区分情景对待。

#### HTML encode

至少对以下HTML字符进行HTML编码：

| **字符** | **编码** |
| :--- | :--- |
| & | &amp;amp; |
| < | &amp;lt; |
| > | &amp;gt; |
| " | &amp;quot; |
| ' | &amp;#x27; |
| / | &amp;#x2F; |

编码后的字符不能执行，也就是：

* `<script>`标签以及其中的内容会以字符串的形式展示出来，而不是以代码形式注入到HTML中
* 标签的属性如`src`以字符串显示，不会自动发送请求 

#### JavaScript encode

* 用`\`对特殊字符`' " < > \ & #`转义
* 变量输出一定要在引号内

例子：

    var x = '"' + encodeJavascript($evil) + '"'
    
    var x = 1;alert(2); // 转义后不在引号内，可以执行alert，引入XSS
    var x = "1\";alert(2);\/\/" // 转义后在引号内，无法执行

#### JSON 转义

* 当 JSON 中包含`U+2028`或`U+2029`这两个字符时，不能作为 JavaScript 的字面量使用，否则会抛出语法错误。

* 当 JSON 中包含字符串`<script>`时，当前的`script`标签将会被闭合，后面的字符串内容浏览器会按照 HTML 进行解析；通过增加下一个`<script>`标签等方法就可以完成注入。


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

### 2.对用户输入字符（包括URL）检查

* 是否过长
* 是否仅包含某组合法字符
* 是否与正则表达式匹配

在用户输入时，由前端过滤用户输入传给后端，这种方法防御XSS是否可行？答案是不可行，因为一旦绕过前端输入，直接构造请求，就可以提交恶意代码了。

后端写入数据库前进行过滤，是否可行？答案是不可行，因为后端这时并不知道内容要输出到哪里，可能是展示，也可能是赋值，过滤会有不确定性，可能产生乱码。

因此对输入的检查只是一种安全辅助手段。

### 3.httponly（防止JavaScript读写Cookie）
### 4.Content Security Policy
### 5.X-XSS-Protection头

## 检测

一、使用通用 XSS 攻击字符串手动检测 XSS 漏洞

* https://github.com/0xsobky/HackVault/wiki/Unleashing-an-Ultimate-XSS-Polyglot

检测代码：

    jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */oNcliCk=alert() )//%0D%0A%0d%0a//</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert()//>\x3e


二、使用扫描工具自动检测 XSS 漏洞。

* https://github.com/Arachni/arachni
* https://github.com/mozilla/http-observatory/
* https://github.com/andresriancho/w3af

## 参考资料

* [【基本功】 前端安全系列之一：如何防止XSS攻击？](https://mp.weixin.qq.com/s?__biz=MjM5NjQ5MTI5OA==&mid=2651748921&idx=2&sn=04ee8977545923ad9b485ba236d7a126&chksm=bd12a3748a652a628ecb841f78e00ccf5eb002117236e18a7d947ae824c2cc75841c1f7c0455&scene=21#wechat_redirect)