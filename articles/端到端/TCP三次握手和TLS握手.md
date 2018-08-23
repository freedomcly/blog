# TCP三次握手和TLS握手

大学时学过《计算机网络》，但对TCP协议的掌握一直一知半解人云亦云。最近在读《High performance browser networking》，也在了解WireShark这个TCP抓包工具，TCP和TLS还是很有意思。

## TCP层

### TCP报文

[详情见wiki](https://en.wikipedia.org/wiki/Transmission_Control_Protocol)

几个重要的概念：

* Flags: 通过（9+3位）控制位作标志，区分SYN、ACK、FIN等
* Sequence number: 序号
* Acknowledgment number: 确认序号

### 三次握手过程

1、主机A -> 主机B

* Flags为SYN（也就是SYN位为1）
* sequence number = x（随机数）
* acknowledgment number = 0

2、主机B -> 主机A

* Flags为SYN，ACK（也就是SYN位和ACK位都为1）
* sequence number = y（随机数）
* acknowledgment number = x + 1

3、主机A -> 主机B

* Flags为ACK（也就是ACK位为1）
* sequence number = x + 1
* acknowledgment number = y + 1

三次握手建立完成，可以正式传输数据了。

### 为什么是三次握手

为了以最小的成本确认两端都能收发数据。

## TLS层

TLS层提供三个基本服务：**加密、身份验证和数据完整性**。

* 加密（非对称加密，对称加密）
* 身份验证（证书）
* 数据完整性（签名）

### HTTPS中的TLS握手

TCP层三次握手完成时，TLS层再进行握手：

4、主机A -> 主机B

* Client Hello

5、主机B -> 主机A

* Server Hello
* Certificate
* Server Public Key
* Server Hello Done

数字证书里包含什么呢？数字签名（把公钥和网站信息用hash算法生成摘要，用证书中心的私钥加密，生成数字签名）、公钥和网站信息。

客户端拿到证书后，可以通过证书中心的公钥对数字签名进行解密，然后把公钥和网站信息用hash算法生成摘要，对比两份摘要是否相符，以判断信息完整性。

用数字证书能证明什么呢？

* 网站经过可信的证书中心的认证
* 网站公钥和证书没有被冒用

6、主机A -> 主机B

* Client Public Key
* Change Cipher Spec

客户端生成一个随机加密串，用服务器公钥加密，传给服务器，作为对称加密串。

7、主机B -> 主机A

* Change Cipher Spec

服务器用自己的私钥解密，得到对称加密串。

接下来，进行加密数据传输。

![](/assets/tls-handshake.png)

### 为什么需要对称加密串

对称加密速度快，性能好。可以测试加密速度：

    openssl speed rsa // 非对称加密
    openssl speed aes // 对称加密




