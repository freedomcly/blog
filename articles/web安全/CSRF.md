# CSRF

cross site request forgery，跨站请求伪造。

攻击者诱导受害者进入第三方网站，在第三方网站中，向被攻击网站发送跨站请求。利用受害者在被攻击网站已经获取的注册凭证，绕过后台的用户验证，达到冒充用户对被攻击的网站执行某项操作的目的。

特点：

* 攻击者不能获取受害者的登录凭证，只是冒用
* 攻击发生在第三方网站，而不是被攻击的网站，被攻击的网站无法阻止攻击发生

**本质：重要操作的所有参数可以被攻击者猜测到。**

## 危险性

* 利用用户身份操作用户账号

## 防御

防御的方案是针对“重要操作的所有参数可以被攻击者猜测到”，构建一个不能被猜到的参数，比如：

* referer check（服务器并非任何时候都能取得referer）
* 验证码（不能加太多验证码，只能当做辅助操作）
* **csrf token**

注意：

* csrf token 保存在哪里？Cookie 还是 Session？
* 打开多个页面，其中一个页面的 csrf token 被消费了，怎么保证其他页面也能顺利交互？

Session 中。