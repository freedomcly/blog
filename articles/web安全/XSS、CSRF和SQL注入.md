# XSS、CSRF和SQL注入

## 一、XSS

cross site script，跨站脚本攻击。**本质：HTML注入**。
XSS分类：

* 反射型XSS：把用户的输入（URL参数等）注入HTML中，往往需要诱使用户点击一个恶意链接
* 存储型XSS：把前端提交的数据（包括恶意代码）未经处理储存在服务器，然后从服务器读取后注入HTML中
* DOM Based XSS：通过修改DOM节点形成的XSS。严格来说也是反射型XSS

#### 危险性

* Cookie劫持（Set-Cookie可以设置httponly，这时JavaScript无法读写Cookie）
* 模拟GET、POST请求

#### 防御

针对不同场景的XSS，区分情景对待。

##### 1.对用户输入字符（包括URL）检查

* 是否过长
* 是否仅包含某组合法字符
* 是否与正则表达式匹配

##### 2.对输出到浏览器的字符检查和编码

**一、HTML encode**

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

**二、JavaScript encode**

* 用`\`对特殊字符`' " < > \ & #`转义
* 变量输出一定要在引号内

    var x = '"' + encodeJavascript($evil) + '"'
    
    var x = 1;alert(2); // 转义后不在引号内，可以执行alert，引入XSS
    var x = "1\";alert(2);\/\/" // 转义后在引号内，无法执行


##### 3.httponly（防止JavaScript读写Cookie）
##### 4.Content Security Policy
##### 5.X-XSS-Protection头

## 二、CSRF

cross site request forgery，跨站请求伪造。如：攻击者诱使用户访问一个页面，就以该用户的身份在第三方站点里执行了一次操作。

**本质：重要操作的所有参数可以被攻击者猜测到。**

#### 危险性

* 利用用户身份操作用户账号

#### 防御

防御的方案是针对“重要操作的所有参数可以被攻击者猜测到”，构建一个不能被猜到的参数，比如：

* **token**
* 验证码（不能加太多验证码，只能当做辅助操作）
* referer check（服务器并非任何时候都能取得referer）

## 三、SQL注入

#### 危险性

非法操作数据库

#### 防御

* 输入检查
