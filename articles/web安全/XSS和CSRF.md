# XSS和CSRF

## XSS

cross site script，跨站脚本攻击。

#### 危险性

* Cookie劫持（Set-Cookie可以设置httponly，这时JavaScript无法获取Cookie）

#### 防御

* httponly
* 对输入（包括URL）检查
* 对输出检查，编码
* Content Security Policy

## CSRF

cross site request forgery，跨站请求伪造

#### 防御

* 验证码
* token
