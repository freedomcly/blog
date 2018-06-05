# XSS和CSRF

## XSS

cross site script，跨站脚本攻击。

### 危险性

* Cookie劫持（Set-Cookie可以设置httponly，这时JavaScript无法获取Cookie）

### 防御

* httponly
* 输入检查和输出检查，使用encode/decode