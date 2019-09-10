Content

- Principles of Secure Communication
  - Confidentiality
  - End-point authentication
  - Message Integrity
- Cryptographic Techniques
  - DES
  - RSA
- Approaches for Message Integrity
  - Message Authentication Codes
  - Digital Signatures
- Endpoint Authentication
  - Nonce
- Network Protocols
  - SSL
  - IPsec
- Operational Security
  - Firewall
  - IDS systems



---

### Main



## 介绍网络安全

传统的网络协议在网络通信时，消息都是明文的传输。如 HTTP，TCP，IP，Ethernet等等协议。然而，明文传输会遭到他人恶意的攻击，这些攻击会窃取客户端用户的隐私信息，破环服务端的工作，和损害我们的电脑等等。常见的安全攻击有：恶意软件攻击，拒绝服务攻击，数据嗅探，IP 欺骗等等。

计算机网络的目的就是通信。如何进行安全有效的通信这是网络安全要实现的功能。

安全的网络通信所需要的属性有：

- 保密性（Confidentiality）。消息进行加密，保证只有通信双方知道消息的内容。
- 消息完整性（Integrity）。保证消息没有被修改。
- 终端认证（End-point Authentication）。确认通信对方的身份。
- 操作安全（Operational Security）。阻止外界对机构网络的恶意网络攻击。

通过网络安全协议可以达到以上所需的功能属性，从而实现安全的网络通信。常见的网络安全协议有 SSL，IPsec 等。

这一小节我们了解了网络安全的基本概念，接下来我们将探究实现网络安全功能的基本技术，以及网络安全协议的实现原理，最后我们将介绍防火墙和入侵侦测系统如和保护一个组织的网络安全。



## 实现网络安全的技术

### 加密算法

#### 对称加密（Symmetric encryption）

分组加密（Block Ciphers）



#### 非对称加密（Asymmetric encryption）

公钥，私钥

### 消息完整性和数字签名



### 终端认证



## 网络安全协议



### SSL



### IPsec



## 操作安全

### 防火墙



### 入侵侦测系统



## References

[1] Computer Networking: A Top-Down Approach, by Jim Kurose