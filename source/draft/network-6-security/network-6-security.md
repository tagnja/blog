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

传统的网络协议在网络通信时，消息都是明文的传输。如 HTTP，TCP，IP，Ethernet等等协议。然而，明文传输会遭到他人恶意的攻击，这些攻击会窃取客户端用户的隐私信息，破环服务端的工作，和损害我们的电脑等等。常见的安全攻击类型有：恶意软件攻击，拒绝服务攻击，数据嗅探，IP 欺骗等等。

计算机网络的目的是为了通信。如何进行安全有效的通信这是网络安全要实现的功能。常见的网络通信安全问题有：

- 消息窃听。
- 消息篡改。插入，删除消息内容。
- 消息伪造和重复消息。

安全的网络通信所需要的必要属性有：

- 保密性（Confidentiality）。消息进行加密，保证只有通信双方知道消息的内容。
- 消息完整性（Integrity）。保证消息没有被修改。
- 终端认证（End-point Authentication）。确认通信对方的身份。
- 操作安全（Operational Security）。阻止外界对机构网络的恶意网络攻击。

通过网络安全协议可以达到以上所需的功能属性，从而实现安全的网络通信。常见的网络安全协议有 PGP，SSL，IPsec 等。

这一小节我们了解了网络安全的基本概念，接下来我们将探究实现网络安全功能的基本技术，以及网络安全协议的实现原理，最后我们将介绍防火墙和入侵侦测系统如和保护一个组织的网络安全。



## 实现网络安全的技术

### 加密算法

加密是指发送端加密的数据，入侵者不能从拦截的数据中获取信息。接收者可以恢复原始的数据。原始消息称为明文（Plaintext），经过加密算法加密后的消息称为密文（Ciphertext）。加密算法通过明文m和密钥 K1 作为输入，产生密文作为输出。密文表示为 `K1(m)`。解密算法通过密文和密钥 K2 作为输入，产生明文作为输出。解密表示为 `K2(K1(m)) = m`。

我们常见把其它操作与加密（Encryption）产生混淆。如编码（Encoding）和消息摘要（Message Digest）。加密需要通过密钥来加密和解密，其他人没有密钥无法进行加密和解密。区分是不是加密的关键因素是：1. 只有使用密钥才能进行相关操作。2. 数据可以加密和还原。

编码是将一种数据格式转换为另一种格式，对应操作为编码和解码。编码任何人都可以操作，它不满足只有使用密钥才能进行相关操作的条件，所以它不是加密算法。常见的编码方式如 Base64。

消息摘要算法。通过 Hash 算法产生一个固定长度的值。它的转换是单向的。常用于数据完整性校验，存储用户密码信息。它没有密钥，也无法还原数据，所以它不是加密算法。常见的消息摘要算法如 MD5。

#### 对称加密（Symmetric Encryption）

对称加密和解密共享唯一的密钥，常见的算法如 DES，3DES，AES。对称加密技术的类型有两种：流加密（Stream Ciphers），分组加密（Block Ciphers）。安全的网络协议 PGP，SSL，IPsec 等大多使用分组加密技术。这里主要介绍分组加密技术。

分组加密：将消息明文划分为等长的块。每一个块独立加密。密文使用一对一映射，将 k bit 长度的明文块转换为 k bit 长度的密文块。一个 k bit 长的密文所有可能的有 2^k !，暴力破解几乎不可能做到。如暴力破解一个 128 位的 AES 密文大约需要100万亿年。 

#### 非对称加密（Asymmetric encryption）

非对称加密使用一对密钥即公钥（Public Key）和私钥（Private Key）来进行加密和解密，常见的算法如 RAS。。它可以让公钥公开在网络上分发，让客户端加密消息，但只有服务端才能解密。非对称加密不仅可以用于加密，也可以用于认证和数字签名。一般的用法时公钥加密，私钥解密。私钥签名，公钥验签。

##### RSA 的实现原理

**生成公钥和私钥过程**

- 选择两个大质数 p 和 q。
- 计算 n = pq, z= (p-1)(q-1)。
- 选择一个数字 e，小于 n，且与 z 没有公共因数。
- 找到一个数字 d，始得 ed - 1 能被 z 整除即 (ed - 1) % z = 0。
- 公钥时一对数字 (n, e)，私钥时(n, d)

**加密和解密过程**

消息明文表示为 m，密文为 c。

加密计算公式： c = m^e % n

解密计算公式： m = c^d % n

例子，

选择 p=5, q=7, n= 35, z=24, e=5, d= 29

若明文为 m=12

加密计算 c = 12 ^ 5 % 35 = 17

解密计算 m = 17^29 % 35 = 12

**RSA 公式工作原理**

基础理论：

- 取模运算转换公式：(a % n)^d % n = a^d % n。

- 数值理论转换。如果 p，q时质数，n=pq，z=(p-1)(q-1)，则 x^y % n = x ^ (y % z) % n。下面应用计算应用 x=m, y=ed.

证明：

RSA 加密公式为：c = m^e % n，解密公式为： m = c^d % n

由 c = m ^ e % n 推导 m = c ^ d % n 过程如下：

（1）由 c = m ^ e % n ，代入等式，得 m = (m ^ e % n) ^ d % n 

（2）由取模运算转换公式，得 m = m ^ ed % n 

（3）由数值理论转换，以及 (ed - 1) % z = 0，得 m = m ^ (ed % z) % n = m ^ 1 % n = m % n

（4）由 m < n，得 m = m。等式左边等于右边，证明原等式成立。



### 消息完整性和数字签名

<br>

#### 介绍消息完整性

认证消息完整性（Message Integrity）需要实现以下功能：

- 验证消息来源自合法的终端。
- 验证消息没有被篡改。

实现消息完整性常见的方式有：数字签名（Digital Signatures）和终端认证（End-point Authentication）。

#### 哈希算法和消息认证码

哈希算法将输入转换为固定长度的哈希值。哈希算法必须任意的两个不同的值不能得到相同的 hash 值。常见的哈希算法有：MD5，Secure Hash Algorithm (SHA-1)。

传统的网络协议使用 checksum 校验和的方式校验数据完整性（数据和校验和相加为n个1），这种方式存在不同的消息可以存在相同的检验和。哈希值有唯一的特性，可以使用消息的哈希值检验完整性，这样更加安全。

##### 消息认证码（Message Authentication Code）

简单的使用哈希进行完整性校验的方式为：将消息 m 的哈希值 H(m) 附加到消息后面，组成新的消息message(m, H(m))。

这样的处理很容易被伪造消息。使用伪造一个新的消息，后面附加新消息的哈希值 message(m', H(m'))，这同样也是一个具有完整性的消息。

为了防止伪造消息，可以使用加密的哈希方法（Cryptographic Hash Function），通信的双方需要共享一个密码 s 称为 认证密钥（Authentication Code）。新的消息可以表示为 message(m+s, H(m+s))。这样入侵者不知道认证密钥，生成不了正确的哈希值。

消息认证码不需要加密算法。

##### 数字签名（Digital Signature）

数字签名是一个加密技术，它指出了一个文档的拥有者。数字签名具有的特性：可验证的，不可伪造的。

数字签名可以使用非对称加密实现。使用私钥加密，使用公钥验证。私钥是被唯一拥有的，公钥可以分发在网络上进行验证。可以对整个消息加密作为数字签名K(m)，但这样太耗费了。更好的做法是对消息的哈希值进行加密作为数字签名K(H(m))。

##### 公钥证书（Public Key Certification）

数字签名的一个重要的应用就是公钥证书。公钥证书它证明一个公钥属于一个具体的实体。公钥证书使用在很多流行的安全网络协议，如 IPsec 和 SSL。

认证机构（Certification Authority, CA），它可以绑定一个公钥和一个特定的实体。它的工作是验证身份和发布证书。

CA 验证实体的身份后，CA 创建一个证书（Certificate），它绑定实体的公钥和身份。证书包含了公钥和实体的认证信息（人的名称，有效期）等等。

### 终端认证

终端认证（End-Point Authentication）指的是在网络上一个实体提供它的身份给另一个实体的过程。

不安全的终端认证方式：

- 明文密码认证。可能被窃听，被他人获取密码。
- 加密密码认证。可能受到回放攻击（Playback Attack）。被他人窃取和利用加密后的密码。

安全的终端认证：

- 使用 nonce 和加密密码。一个 nonce 是一个数字仅仅在网络通信中使用一次，即每次通信使用不同的 nonce。这样可以防止他人利用加密后的密码。

## 网络安全协议

### SSL

Secure Sockets Layer（SSL）是一个传输层协议，提供增强 TCP 的安全服务。SSL 提供了保密性，完整性，服务器认证，客户端认证等服务。一个 SSL 轻微修改版本 SSL version 3 称为 Transport Layer Secure（TLS）。

SSL 建立连接的过程

- 三次握手
  - 客服端，发送 SYN。
  - 服务端，响应 SYN/ACK
  - 客服端，发送 ACK。
- 协商（Negotiation）
  - 客户端发送 Hello，以及支持的加密算法列表，和客户端 nonce（Random Number #1）。
  - 服务端响应，发送服务端选择的一个对称加密算法，一个非对称加密算法，一个 MAC 算法，SSL 证书，和服务端 nonce（Random Number #2）。
  - 客服端，验证证书，提取服务端公钥，生成一个 Pre-Master Secret（PMS），使用公钥加密 PMS，发送 PMS 和客户端证书（可选项）给服务端。
  - 服务端，使用私钥解密 Pre-Master Secret。
- 共享密码
  - 客户端和服务端，通过随机数 #1，#2 和  PMS 计算得到 Master Secret（MS）。使用MS 生成 两个 加密密钥，两个 MAC 密钥。
- 握手完成
  - 客服端，发送一个哈希消息使用主密码（MS）
  - 服务端，发送一个哈希消息使用主密码（MS）

SSL 

- 保密性
  - SSL 使用对称加密交换共享的对称加密密钥。
  - SSL 使用对称加密密钥机密数据。
- 完整性
  - 使用 MAC 进行完整性校验。
  - 通过 CA 验证公钥的合法性。认证消息来自合法的终端。
- 服务端认证
  - 通过 CA 验证公钥的合法性。从而认证服务端。

- 防止回放攻击
  - 使用 nonce 防御连接回放攻击（Connection Playback Attack）
  - 使用MAC key + Sequence Number 生成MAC，防御数据包传输途中的回放攻击。

### IPsec



### HTTPS



## 操作安全

### 防火墙



### 入侵侦测系统



## References

[1] Computer Networking: A Top-Down Approach, by Jim Kurose

[2] [Base64 - Wikipedia](https://en.wikipedia.org/wiki/Base64)

[3] [MD5 - Wikipedia](https://en.wikipedia.org/wiki/MD5)