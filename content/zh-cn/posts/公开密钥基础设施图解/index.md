---
title: "公开密钥基础设施图解"
date: 2016-09-30
categories:
  - "engineering"
tags:
  - "网络安全"
  - "pki"
  - "公开密钥"
  - "公开密钥基础设施"
  - "对称密钥"
  - "数字证书"
  - "短摘要"
  - "私有密钥"
---

很久以来，一直搞不清楚各种加密算法、哈希函数的应用场景，直到读了“[深入理解加密、解密、数字签名和数字证书](http://blog.csdn.net/sszgg2006/article/details/8199175)”这篇博文后，一切概念立刻变得明朗起来，于是赶快画了一张图捋清了思路，方便自己和看到这篇文章的朋友回顾。因为原文作者写得足够精彩，逻辑性很强，所以在此只简略叙述图中所画内容。

[![basics-of-public-key-infrastrature](/wp-content/uploads/2016/09/basics-of-public-key-infrastrature.png)](/wp-content/uploads/2016/09/basics-of-public-key-infrastrature.png)

 

A要通过网络将文件（Original File）发送给B，为了能让B安全收到，避免传输过程中被截获、窃听或伪造，目前相对安全的做法是图中描述的流程。

A需要执行的操作：

```
(1) 用对称密钥（Symmetric Key）通过对称加密算法把文件加密成为密文文件（Encrypted File, or Cypher Text）；
(2) 用B的公开密钥（Public Key）通过非对称加密算法把对称密钥加密成为加密的对称密钥（Encrypted Symmetric Key）；
(3) 通过哈希算法生成文件的短摘要（Short Digest）；
(4) 用自己的私有密钥（Private Key）通过非对称加密算法对短摘要签名，生成签名摘要；
(5) 将自己的公开密钥和个人信息交付CA机构认证获取数字证书（Certificate）。

最后将密文文件、加密的对称密钥、签名摘要和数字证书通过网络传给B。
```

B收到A发来的文件需要执行的操作：

```
(1) 用自己的私有密钥对A发来的加密的对称密钥解密，获得解密的对称密钥（Decrypted Symmetric Key）；
(2) 用解密的对称密钥解密密文文件，获得解密文件（Decrypted File）；
(3) 用相同的哈希算法，生成解密文件的短摘要作为待测摘要（Tested Short Digest）；
(4) 用A发来的数字证书获取A的公开密钥；
(5) 用A的公开密钥对签名摘要解密得到解密的摘要（Decrypted Short Digest）；
(6) 对比待测摘要和解密摘要，如果完全一致，说明解密文件就是原始文件，如果不一致，说明解密文件是伪造文件。
```
