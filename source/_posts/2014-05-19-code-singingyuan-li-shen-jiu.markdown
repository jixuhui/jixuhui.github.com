---
layout: post
title: "code signing原理深究"
date: 2014-05-19 17:44:13 +0800
comments: true
categories:
- iOS code signing
---

## 理解之前需要了解 Cryptographic Services （加密服务）
https://developer.apple.com/library/ios/documentation/Security/Conceptual/Security_Overview/CryptographicServices/CryptographicServices.html#//apple_ref/doc/uid/TP30000976-CH3-SW1该文章主要介绍常用的四种加密方案：

1. 对称加密 【sender和receiver是一个密钥，速度快，但是传递密钥存在安全隐患，需要建立独立的安全通道】
2. 非对称加密 【公钥、私钥，公钥加密则私钥解密 and versa vice，保证了安全性，公钥发送不需要特殊的安全通道，此方案可以做为传递对称加密密钥的方案，速度较对称加密慢】
3. 极速加密
4. 散列hash加密

## 而后，应该是苹果早期的sdk文档介绍code signing：【主要介绍关于certificate】
http://blog.sina.com.cn/s/blog_45d0b2cb0100n7sq.html需要通读全文，方有深入理解

1. 完成apple id注册和申请developer后
2. Certificate Signing Request （CSR），在进行请求后即将公钥传送给apple服务器，而后生成一个.cer文件【有公钥签名的】和一个私钥，这个公钥签名的.cer文件和该私钥统一叫做数字身份或者签名身份【digital identity or signing identity】
3. 以后无论申请还是签名你开发的application都是用这个siging identity
4. Copying a Signing Identity To Another Computer，你从网上下载的signing certificate只是有公钥，没有私钥，所以完成不了application签名，
Instead, use the Export Items menu item in the File menu of Keychain Access to export both the certificate and private key as a Personal Information Exchange (.p12) file
and copy that file to the second computer. Double-click the file to install the certificate and key in the keychain.

## About Code Signing
主要是一张图:https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40005929-CH1-SW1
1. 通过理解第二大部分的内容，这个图基本理解，图中有散列哈希后生成的message digest【摘要信息】，对它用私钥进行加密生成digital signature
2. 最后，Data + signing certificate + digital signature = code-signed data
