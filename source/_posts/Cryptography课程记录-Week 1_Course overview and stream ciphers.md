---
title: "Cryptography课程记录-Week 1: Course overview and stream ciphers"
date: 2023-12-04 14:39:54
tags:
  - Cryptography
categories: 课程笔记
---
# Section 1: what is cryptography about?
## Course Overview
课程目标：
- 密码学原理
- 如何正确使用密码学原语和安全性证明

密码学的应用案例：
- 安全通信：Alice和Bob试图使用HTTPS通信，起到安全作用的是SSL，也称为TLS，确保网络中传输的数据既不会被窃听也不会被篡改。
	- 网络通信：HTTPS
	- 无线通信：802.11i WPA2, GSM, Bluetooth
	- SSL(Secure Sockets Layer)/TLS由两部分组成：
		1. 握手协议：构造共享密钥，采用公钥技术
		2. 记录层：如何使用密钥传输数据
- 磁盘加密文件：EFS, TrueCrypt
	- 对磁盘上的文件加密，保证既不会被窃取也不会被篡改【保密性和完整性】
- 内容保护（例如DVD, Blu-ray）：CSS, AACS
- 用户认证 等等

安全通信的基石：对称加密系统（sym.encryption）
- Alice和Bob使用共享密钥`k`通信，攻击者不知道`k`
- 使用公开的加密算法`E`和解密算法`D`，`E`的输入是明文消息`m`和密钥`k`，输出密文消息`c`；`D`的输入是密文消息`c`和密钥`k`，输出明文消息`m`。

对称加密系统的两种情况：
- 一次性密钥：只用来加密单条消息，例如加密邮件的时候，每个邮件都生成一个新密钥
- 重复使用密钥：用来加密多条消息，例如加密文件的时候，一个密钥用于若干个文件，需要一些额外工作确保这样的加密系统是安全的

密码学的局限性：
- 并不能完全保证安全，对软件漏洞和社工无效
- 需要正确使用，一个例子是加密WiFi通信的WEP
- 使用密码学原语，而非创造

## What is Cryptography?
密码学核心：安全通信
- 安全密钥构造
- 安全通信

密码学的其他应用：
- 数字签名
- 匿名通信
- 匿名数字货币
	- 支付数字货币而保持匿名
	- 实现该功能的同时避免双花
- 结合抽象协议
	- 选举：既知道被选中的是谁，又不知道每个人投了谁——引入选举中心计票
	- 私有拍卖：采用Victory拍卖（价高者得，二价支付），需要公开出价最高的身份和出价第二高的价格，其余信息保密——引入拍卖中心统计
	- 安全多方计算：输出函数计算的值，而对函数输入信息保密，例如前两个涉及的选票计算和拍卖计算——在密码学中可信第三方是不可靠的，系统也不应当引入这样的第三方，而是通过一些协议完成计算。
- 其他不太相关的应用
	- 私有外包计算：Alice将一串关键词加密后发送给Google，Google无需知道原文即可根据加密关键词检索到加密结果并返回给Alice，Alice自行解密得到查询结果。
	- 零知识证明：Alice知道一个大数字N，也知道该数字可由两个素数P和Q相乘得到；Bob也知道N，但不知道P和Q，Alice可通过某方法向Bob证明自己确实知道P和Q，而无需让Bob也知道P和Q。

密码学的三个步骤：
- 精确说明威胁模型，例如数字签名中的威胁模型是：攻击者如何对数字签名发起攻击，攻击者发动攻击的动机
- 提出构造方案
- 证明在特定威胁模型条件下，任何攻击者对该构造发起攻击都相当于求解一个很复杂的难题。

## History of Cryptography
Alice和Bob试图安全对话，攻击者试图窃听，为确保安全，A和B使用加密器`(E,D)`，前者是加密算法，后者是解密算法。

对称加密：加密和解密使用相同的密钥：
- $c=E(k,m)$表示用算法$E$和密钥$k$对消息$m$加密得到密文$c$
- $m:=D(k,c)$表示用算法$D$和密钥$k$对密文$c$解密得到消息$m$

一些已破解的对称加密：
- 替换加密：不同字母之间替换	
- 凯撒密码是类似的方式。破解方式：字母频率、字母组合频率
- Vigener密码：密钥复制到明文的长度，明文的每一位和密钥的每一位相加再模26，破解方式也是频率
- 转轮机（Rotor machine）
- 数据加密标准DES

一些仍有效的密码：AES、Salsa20 等






## Section 2: crash course in discrete probability
### Discrete Probability

## Stream Ciphers 1: the one-time pad and stream ciphers
### Information Theoretic Security and The One Time Pad

### Stream Ciphers and Pseudo Random Generators


