# XSS

cross site script，跨站脚本攻击。**本质：HTML注入**。

XSS分类：

* 反射型XSS：把用户的输入（URL参数等）注入HTML中，往往需要诱使用户点击一个恶意链接
* 存储型XSS：把前端提交的数据（包括恶意代码）未经处理储存在服务器，然后从服务器读取后注入HTML中
* DOM Based XSS：通过修改DOM节点形成的XSS。严格来说也是反射型XSS

## 危险性

* Cookie劫持（Set-Cookie可以设置httponly，这时JavaScript无法读写Cookie）
* 模拟GET、POST请求

## 防御

针对不同场景的XSS，区分情景对待。

### 1.对用户输入字符（包括URL）检查

* 是否过长
* 是否仅包含某组合法字符
* 是否与正则表达式匹配

### 2.对输出到浏览器的字符检查和编码

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

    var x = '"' + encodeJavascript($evil) + '"'
    
    var x = 1;alert(2); // 转义后不在引号内，可以执行alert，引入XSS
    var x = "1\";alert(2);\/\/" // 转义后在引号内，无法执行

### 3.httponly（防止JavaScript读写Cookie）
### 4.Content Security Policy
### 5.X-XSS-Protection头

