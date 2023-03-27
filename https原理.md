---
title: https+TLS
date: 2023-2-21 13:38:45
---



# https原理

## 0x00前置知识

### 1.对称加密

加锁和解锁用的是同一把钥匙

![img](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/851282a6599be640caa822804e192ec2.gif) 

由于中间人没有钥匙，无法偷窥和篡改

但密钥在传输的过程中可能泄露，并没有完全解决问题



### 2.非对称加密

**用钥匙 A 加锁，必须用钥匙 B 才能解锁。反过来用钥匙 B 加锁，必须用钥匙 A 才能解锁。**自己保留其中一把，将另一把给对方

![img](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/0c649c404026647ebcd999a30dafeb75.gif) 

中间人不能篡改消息，因为没有加密密钥，如果篡改接收方不再能使用原来的密钥解锁

但无法解决内容被偷窥即内容泄露，因为解锁的密钥大家都有



### 3.对称加密+非对称加密

对方生成两把密钥，将其中一把传给我，我在生成一个钥匙，用对方给的密钥加密，在传给对方。**即用非对称加密加密对称加密的密钥**

![img](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/66b748fff608b82656585af31cc9b6ae.png) 

解决了信息泄露和篡改问题，因为D加密的密钥只有C能解开，因为对称密钥可以安全传递，由于中间人无法解密对称密钥，后续传输的消息也无法解密

但我们不能验证传来的D密钥一定就是对方给我的，即客户端如何识别服务端的合法性的问题没有解决

如果中间人自己生成了一堆密钥，在对方给我D时截获，把自己的密钥Y给我，我并不知情，用Y加锁了M，中间人就能在通过自己生成的x解密，得到对称加密的密钥，从而后续消息全部被泄露

![image-20230223094902707](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/image-20230223094902707.png)  



### 4.CA

引入可信的第三机构，即此处条件时第三方绝对可信，要是你不相信那我就不和你玩啦

![image-20230223094951762](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/image-20230223094951762.png)

第三方机构生成两把密钥，将公钥公开。下次传输时我们先找第三方让他用私钥给我们的公钥加密，加密后在传给对方，由于第三方的公钥大家都有，我就能使用第三方的公钥解密发来的公钥，即由于没有第三方的私钥，中间人不能篡改密钥，因为他们没有第三方的私钥，无法加锁，那么后续我就能用非对称加密的密钥加锁对称加密的密钥，实现0x03最开始的步骤



### 5.总结一下

1.这个过程中，小宇就是服务端，我就是客户端，班长就是CA

2.对于服务端来说，他使用的是公钥加密，私钥解密，这个叫做加密。对于CA来说私钥加密，公钥解密，这个叫做签名

3.对称加密，对称加密的速度很快，可以用于传输过程中的数据加密，防止中间人查看和篡改信息。但是如何将对称加密的秘钥安全传递过去，是个问题。

4.双钥匙就是非对称加密，非对称加密的速度慢，可以用于加密少量数据，同时也可以用于签名防止篡改

5.



## TLS

HTTP 由于是明文传输，所谓的明文，就是说客户端与服务端通信的信息都是肉眼可见的，随意使用一个抓包工具都可以截获通信的内容。

HTTP**S** 在 HTTP 与 TCP 层之间加入了 TLS 协议，来解决窃听，篡改，冒充的风险。

![image-20230223093249740](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/image-20230223093249740.png) 

TLS 协议是如何解决 HTTP 的风险的呢？

- *信息加密*：HTTP 交互信息是被加密的，第三方就无法被窃取；
- *校验机制*：校验信息传输过程中是否有被第三方篡改过，如果被篡改过，则会有警告提示；
- *身份证书*：证明淘宝是真的淘宝网；

![image-20210126102630933](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/dkBwYObE91VMUpC.png) 

HTTPS 是应用层协议，需要先完成 TCP 连接建立，然后走 TLS 握手过程后，才能建立通信安全的连接。



## RSA密钥协商握手过程

![图片](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/mQadqwHV3EMJogv.png)

![图片](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/VTmwlMEOsI1rnPg.png)



### TLS第一次握手

客户端首先会发一个「**Client Hello**」消息

消息里面有客户端使用的 TLS 版本号、支持的密码套件列表，支持的压缩算法，以及生成的**随机数（\*Client Random\*）**，这个随机数会被服务端保留，它是生成对称加密密钥的材料之一。

![图片](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/ka4Yr6OQmNPpDhL.png) 



### TLS第二次握手

当服务端收到客户端的「Client Hello」消息后，会确认 TLS 版本号是否支持，和从密码套件列表中选择一个密码套件，还有选择压缩算法（安全性原因，一般不压缩），以及生成**随机数（\*Server Random\*）**。

接着，返回「**Server Hello**」消息，消息里面有服务器确认的 TLS 版本号，也给出了随机数（Server Random），然后从客户端的密码套件列表选择了一个合适的密码套件。

![图片](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/TtrAV1xlXLRuH5M.png) 

密码套件的基本形式是：密钥交换算法 + 签名算法 + 对称加密算法 + 摘要算法

其实这两个随机数是后续作为生成「会话密钥」的条件，所谓的会话密钥就是数据传输时，所使用的对称加密密钥。

服务端为了证明自己的身份，会发送「**Server Certificate**」给客户端，这个消息里含有数字证书。

![图片](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/48MXspeJuiRlfqT.png) 

随后，服务端发了「**Server Hello Done**」消息

![image-20210831144857433](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/h8t2PL745AFS1uo.png) 





### TLS第三次握手

客户端验证完证书后，认为可信则继续往下走。接着，客户端就会生成一个新的**随机数 (pre-master)**，用服务器的 RSA 公钥加密该随机数，通过「**Change Cipher Key Exchange**」消息传给服务端。

服务端收到后，用 RSA 私钥解密，得到客户端发来的随机数 (pre-master)。

至此，**客户端和服务端双方都共享了三个随机数，分别是 Client Random、Server Random、pre-master**。

于是，双方根据已经得到的三个随机数，生成**会话密钥（Master Secret）**，它是对称密钥，用于对后续的 HTTP 请求/响应的数据加解密。

生成完会话密钥后，然后客户端发一个「**Change Cipher Spec**」，告诉服务端开始使用加密方式发送消息。

![图片](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/LiAoKdyMlIOrJjQ.png) 

然后，客户端再发一个「**Encrypted Handshake Message（Finishd）**」消息，把之前所有发送的数据做个摘要，再用会话密钥（master secret）加密一下，让服务器做个验证，验证加密通信是否可用和之前握手信息是否有被中途篡改过。

Change Cipher Spec」之前传输的 TLS 握手数据都是明文，之后都是对称密钥加密的密文。





### TLS 第四次握手

服务器也是同样的操作，发「**Change Cipher Spec**」和「**Encrypted Handshake Message**」消息，如果双方都验证加密和解密没问题，那么握手正式完成。

最后，就用「会话密钥」加解密 HTTP 请求和响应了。



### 第二阶段客户端核验证书

一个数字证书通常包含了：

- 公钥；
- 持有者信息；
- 证书认证机构（CA）的信息；
- CA 对这份文件的数字签名及使用的算法；
- 证书有效期；
- 还有一些其他额外信息；

服务端的证书都是由 CA （*Certificate Authority*，证书认证机构）签名的，具有极高的可信度，所以由它来给各个公钥签名，信任的一方签发的证书，那必然证书也是被信任的。



数字证书签发和验证流程

![图片](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/dVPZvSjxRzCwWU1.png) 

> CA 签发证书的过程，如上图左边部分：
>
> - 首先 CA 会把持有者的公钥、用途、颁发者、有效时间等信息打成一个包，然后对这些信息进行 Hash 计算，得到一个 Hash 值；
> - 然后 CA 会使用自己的私钥将该 Hash 值加密，生成 Certificate Signature，也就是 CA 对证书做了签名；
> - 最后将 Certificate Signature 添加在文件证书上，形成数字证书；
>
> 客户端校验服务端的数字证书的过程，如上图右边部分：
>
> - 首先客户端会使用同样的 Hash 算法获取该证书的 Hash 值 H1；
> - 通常浏览器和操作系统中集成了 CA 的公钥信息，浏览器收到证书后可以使用 CA 的公钥解密 Certificate Signature 内容，得到一个 Hash 值 H2 ；
> - 最后比较 H1 和 H2，如果值相同，则为可信赖的证书，否则则认为证书不可信。



![image-20230327115335014](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/image-20230327115335014.png) 







### 证书链

我们向 CA 申请的证书一般不是根证书签发的，而是由中间证书签发的，比如百度的证书，从下图你可以看到，证书的层级有三级：

对于这种三级层级关系的证书的验证过程如下：

- 客户端收到 baidu.com 的证书后，发现这个证书的签发者不是根证书，就无法根据本地已有的根证书中的公钥去验证 baidu.com 证书是否可信。于是，客户端根据 baidu.com 证书中的签发者，找到该证书的颁发机构是 “GlobalSign Organization Validation CA - SHA256 - G2”，然后向 CA 请求该中间证书。
- 请求到证书后发现 “GlobalSign Organization Validation CA - SHA256 - G2” 证书是由 “GlobalSign Root CA” 签发的，由于 “GlobalSign Root CA” 没有再上级签发机构，说明它是根证书，也就是自签证书。应用软件会检查此证书有否已预载于根证书清单上，如果有，则可以利用根证书中的公钥去验证 “GlobalSign Organization Validation CA - SHA256 - G2” 证书，如果发现验证通过，就认为该中间证书是可信的。
- “GlobalSign Organization Validation CA - SHA256 - G2” 证书被信任后，可以使用 “GlobalSign Organization Validation CA - SHA256 - G2” 证书中的公钥去验证 baidu.com 证书的可信性，如果验证通过，就可以信任 baidu.com 证书。

最开始客户端只信任根证书 GlobalSign Root CA 证书的，然后 “GlobalSign Root CA” 证书信任 “GlobalSign Organization Validation CA - SHA256 - G2” 证书，而 “GlobalSign Organization Validation CA - SHA256 - G2” 证书又信任 baidu.com 证书，于是客户端也信任 baidu.com 证书。

为了确保根证书的绝对安全性，将根证书隔离地越严格越好，不然根证书如果失守了，那么整个信任链都会有问题。

![图片](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/Szq7jLfxumFhOWc.png) 





## RSA缺陷

**使用 RSA 密钥协商算法的最大问题是不支持前向保密**。因为客户端传递随机数（用于生成对称加密密钥的条件之一）给服务端时使用的是公钥加密的，服务端收到后，会用私钥解密得到随机数。所以一旦服务端的私钥泄漏了，过去被第三方截获的所有 TLS 通讯密文都会被破解。

DH解决了RSA的问题，**即使第三方截获了 TLS 握手阶段传递的公钥，在不知道的私钥的情况下，也是无法计算出密钥的，而且每一次对称加密密钥都是实时生成的，实现前向保密**。

但因为 DH 算法的计算效率问题，后面出现了 ECDHE 密钥协商算法，我们现在大多数网站使用的正是 ECDHE 密钥协商算法。





### 再总结一下

假设下面客户端和服务端使用的是RSA密钥交换算法

```
1.客户端和服务端进行tcp三次握手

-----------第一次握手---------------

2.客户端发送client hello，其中携带随机数C，TLS版本号，支持的密码套件列表

-----------第一次握手结束-------------

服务端发送ack

-----------第二次握手---------------

3.服务端生成随机数S，确认TLS版本号，使用的密码套件，放在server hello中回给客户端

4.服务端发送server certificate，其中包含自己的数字证书

5.服务端发送server hello done，本地hello完毕

-----------第二次握手结束-------------

-----------客户端验证证书---------------

6.客户端使用第二次握手中协商的hash算法对证书进行hash计算得到一个hash值

7.用CA公钥解密证书中的certificate signature.(证书中的certificate signature是证书通过相同的额hash算法在经过CA私钥加密得到的)

8.如果6和7中两次hash计算得到的hash不一样，那么认为证书不可信，如果可信，进行第三次握手

-----------客户端验证证书结束----------

-----------第三次握手---------------

9.客户端生成一个新的随机数(pre-master)，用服务端的RSA公钥加密，通过Change Cipher Key Exchange传给服务器

10.服务端用RSA私钥解密，得到Pre-master

11.双方根据 Client Random(C)、Server Random(S)、pre-master 生成对称加密的密钥

之后发送的都是对称密钥加密的密文

12.客户端发送Change Cipher Spec 告诉我服务端开始使用加密方式发送信息

13.客户端发送Encrypted Handshake Message（Finishd）消息，把之前发送数据做hash，在使用11生成的密钥加密，发给服务器做验证，验证加密通信是否可用和之前握手信息是否有被中途篡改过

-----------第三次握手结束-------------

-----------第四次握手---------------

14.服务端发送Change Cipher Spec 告诉我服务端开始使用加密方式发送信息

15.服务端发送Encrypted Handshake Message（Finishd）消息，把之前发送数据做hash，在使用11生成的密钥加密，发给客户器做验证，验证加密通信是否可用和之前握手信息是否有被中途篡改过
```



## SSL和TLS区别

SSL

Secure Socket Layer，安全套接字层。SSL为应用程序提供加密数据通道，它采用了RC4、MD5以及RSA等加密算法，使用40 位的密钥，适用于商业信息的加密。

SSL协议组成：SSL记录协议+ SSL握手协议

记录协议（SSL Record Protocol）：它建立在可靠的传输协议（如TCP）之上，为高层协议提供数据封装、压缩、加密等基本功能的支持。

SSL握手协议（SSL Handshake Protocol）：它建立在SSL记录协议之上，用于在实际的数据传输开始前，通讯双方进行身份认证、协商加密算法、交换加密密钥等。


TLS

Transport Layer Security，安全传输层协议。IETF对SSL3.0进行了标准化，并添加了少数机制(但是几乎和SSL3.0无差异)，标准化后的IETF更名为TLS1.0

TLS协议组成：TLS记录协议+ TLS握手协议

TLS是独立于应用层协议，如何启动 TLS 握手协议以及如何解释交换的认证证书的决定权留给协议的设计者和实施者来判断。

TLS 记录协议：是一种分层协议，支持信息传输、将数据分段到可处理块、压缩数据、应用MAC 、加密以及传输结果等。对接收到的数据进行解密、校验、解压缩、重组等，然后将它们传送到高层客户机

TLS 握手协议：由三个子协议组构成，允许对等双方在记录层的安全参数上达成一致、自我认证、例示协商安全参数、互相报告出错条件。



TLS和SSL区别

> TLS的产生是为了让SSL更安全，使协议更加精确和完善。TLS在SSL3.0基础上增强了其他内容。它们的最主要的差别是所支持的加密算法不同，TLS和SSL3.0不能互相操作，TLS相当于SSL 3.1
>
> TLS的记录格式与SSL一样，但版本号不同，TLS的版本使用的是SSL 3.1。TLS就是以3.0为基础定义的。
>
> **报文鉴别码不一样**。TLS使用了RFC-2104定义的HMAC算法，SSL3.0使用了类似的算法，虽然安全程度一样，但具体算法不一样，填充字节和秘钥之间采用的是连接运算，而HMAC采用的是异或运算。
>
> **伪随机数函数不一样**。TLS使用了PRF伪随机函数将秘钥扩展成数据块，PRF使用两种散列算法保证其安全性，只有两种算法都暴露数据才会不安全。
>
> **TLS有更严密的警报**。除了SSL的报警代码，TLS还补充了很多报警代码，如解密失败，记录溢出，拒绝访问等
>
> **密文和客户证书**：TLS不支持Fortezza秘钥交换、加密算法和客户证书。
>
> 对于消息的认证采用秘钥散列法(HMAC)：当消息在开放的网络上传输时，通过HMAC确保传输的信息不会被篡改。HMAC比SSL3.0的消息认证法MAC更安全。
>
> **增强的伪随机公能**(PRF)：HMAC定义PRF,用于生成秘钥数据。PRF使用两种散列算法保证其安全性，只有两种算法都暴露数据才会不安全。





## TLS 1.2和TLS 1.3

目前低版本的 TLS （例如：SSL 3.0/TLS 1.0 等）存在许多严重漏洞。

TLS 1.3 与之前的协议有较大差异，主要在于：

> - 相比过去的的版本，引入了新的密钥协商机制 — PSK
> - 支持 0-RTT 数据传输，在建立连接时节省了往返时间
> - 废弃了 3DES、RC4、AES-CBC 等加密组件，废弃了 SHA1、MD5 等哈希算法
> - ServerHello 之后的所有握手消息采取了加密操作，可见明文大大减少
> - 不再允许对加密报文进行压缩、不再允许双方发起重协商
> - DSA 证书不再允许在 TLS 1.3 中使用

TLS 1.2

![img](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/v2-4da93c38f9b07d63413a2d05dbb49d43_720w.webp) 

TLS 1.3

![img](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/v2-92d195e62d01c0e085952c6e6c90a75d_720w.webp) 



使用 TLS 1.2 需要两次往返（ 2-RTT ）才能完成握手

TLS 1.3 的握手不再支持静态的 RSA 密钥交换，这意味着必须使用带有前向安全的 Diffie-Hellman 进行全面握手。使用 TLS 1.3 协议只需要一次往返（ 1-RTT ）就可以完成握手。









[SSL与TLS到底有何区别，一见分晓_luo_boke的博客-CSDN博客_ssl和tls区别](https://blog.csdn.net/luo_boke/article/details/114220450)

[TLS1.3 VS TLS1.2，让你明白TLS1.3的强大 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/44980381)