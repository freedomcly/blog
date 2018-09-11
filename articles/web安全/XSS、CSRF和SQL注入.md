# XSS、CSRF和SQL注入

## 一、XSS

cross site script，跨站脚本攻击。**本质：HTML注入**。
XSS分类：

* 反射型XSS：把用户的输入（URL参数等）注入HTML中
* 存储型XSS：把前端提交的数据（包括恶意代码）未经处理储存在服务器，然后从服务器读取后注入HTML中
* DOM Based XSS：通过修改DOM节点形成的XSS。严格来说也是反射型XSS

#### 危险性

* Cookie劫持（Set-Cookie可以设置httponly，这时JavaScript无法获取Cookie）

#### 防御

* 对输入（包括URL）检查
* 对输出的HTML检查，htmlEncode
* httponly
* Content Security Policy

## 二、CSRF

cross site request forgery，跨站请求伪造

#### 危险性

* 利用用户身份操作用户账号

#### 防御

* 验证码
* token

## 三、SQL注入

#### 危险性

非法操作数据库

#### 防御

* 输入检查
