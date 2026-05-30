---
title: "数字签名在安卓包发布中的应用"
date: 2016-10-11
categories:
  - "engineering"
tags:
  - "网络安全"
  - "digital-certificate"
  - "digital-signature"
  - "identity-certificate"
  - "private-key"
  - "public-key"
  - "public-key-certificate"
  - "short-digest"
  - "公开密钥"
  - "公钥证书"
  - "原始摘要"
  - "数字签名"
  - "数字证书"
  - "私有密钥"
  - "签名摘要"
  - "身份证书"
---

安卓包发布前必须要有正式签名才能在应用商店发布，对照[公开密钥基础设施](http://mjm1990.com/article/%E5%85%AC%E5%BC%80%E5%AF%86%E9%92%A5%E5%9F%BA%E7%A1%80%E8%AE%BE%E6%96%BD%E5%9B%BE%E8%A7%A3)的常规流程而言，安卓包签名仅是个子集，只有数字签名，不涉及加密。

数字签名中核心的要素是私有密钥、公钥证书、原始摘要和签名摘要。涉及的参与者有应用开发者、应用商店和用户（操作系统）。逐一了解了每个核心要素和参与者也就清楚了整个签名的动机和操作步骤。

数字签名是拿**私有密钥**对原始摘要（不管是文件还是对称密钥本质上都是字符串）进行加密，利用大素数的一些特性对原字符串进行变换，得到签名摘要。这个签名摘要，只有用对应的公开密钥才能恢复为原始摘要。

公开密钥是**公钥证书**的主要内容，但不是全部。公钥证书中还写明了应用开发者的基本信息包括开发者组织名称、组织所在地、证书失效时间等信息。公钥证书又叫数字证书和身份证书。身份证书的称谓也反映了这个证书是用来识别应用来源身份的。公钥证书需要被权威机构认证，对于安卓而言，这个组织就是Google Play和国内各大应用商店。各大应用商店需要验证应用开发者每次的公钥证书是否一致，不一致的证书就可能遭到拒绝。除此以外，还需要对签名摘要拿公开密钥解密获得原始摘要，并且同原始摘要进行比对，但凡发现不一致的地方，就可能是文件遭到了篡改，同样会拒绝应用发布。实际上操作系统同样会验证公钥证书的一致性和签名摘要的完整性，仅会提醒，但不拒绝安装有风险的安卓包。是否安装有风险的包就取决于用户自己了。

前面的描述中提到对比解密后的**签名摘要**和**原始摘要**可以发现文件是否遭篡改。很容易产生的疑问是为什么比对摘要就可以发现这点？要回答这个问题，就需要了解摘要是什么。安卓包中的原始摘要是一个字符串的集合，每个字符串对应包中的一个文件，每个字符串都是通过哈希散列算法对文件计算出的一个短序列。这个字符串的集合要求包中的文件一个都不能少，也一个都不能多。这个集合中不包含原始摘要文件自己、签名摘要和公钥证书。签名摘要也是类似于原始摘要的一个字符串集合，只是每个短序列是都用公开密钥签名后的结果，此外还增加了原始摘要文件的短序列和签名序列。

私有密钥由开发者秘密保存，公钥证书、原始摘要和签名摘要随签名包一起发布。实际上签名包和未签名包的差别就是公钥证书、原始摘要和签名摘要。在签名包中，一般放在META-INF中，后缀一般是CER、MF和SF。

###### 安卓包签名示意图：

[![apk-signing-process](/wp-content/uploads/2016/10/apk-signing-process.png)](/wp-content/uploads/2016/10/apk-signing-process.png)

###### 相关阅读：

- [Sign Your App](https://developer.android.com/studio/publish/app-signing.html)?⭐️⭐️⭐️⭐️⭐️
- [深入理解加密、解密、数字签名和数字证书](http://blog.csdn.net/sszgg2006/article/details/8199175)?⭐️⭐️⭐️⭐️⭐️
- [Differentiate between apk and jar files | the comment by?user229002](http://askubuntu.com/questions/167095/differentiate-between-apk-and-jar-files)?⭐️⭐️⭐️⭐️⭐️
- [android APK签名过程之MANIFEST.MF分析](http://blog.csdn.net/asmcvc/article/details/9311827)?⭐️⭐️⭐️
- [How can I export my private key from a Java Keytool keystore?](http://security.stackexchange.com/questions/3779/how-can-i-export-my-private-key-from-a-java-keytool-keystore)?⭐️⭐️⭐️
