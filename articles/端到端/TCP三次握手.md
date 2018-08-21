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

### HTTPS中的TLS握手

TCP层三次握手完成时，TLS层再进行握手：

4、主机A -> 主机B

* Client Hello

5、主机B -> 主机A

* Server Hello
* Certificate
* Server Public Key
* Server Hello Done

6、主机A -> 主机B

* Client Public Key
* Change Cipher Spec

客户端生成一个随机加密串，用服务器公钥加密，传给服务器，作为对称加密串。

7、主机B -> 主机A

* Change Cipher Spec

接下来，进行数据传输。

![](/assets/tls-handshake.png)

### 为什么需要对称加密串

对称加密速度快，性能好。可以测试加密性能：

    openssl speed rsa // 非对称加密
    openssl speed aes // 对称加密




