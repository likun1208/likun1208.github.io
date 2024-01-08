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
定义：$U$：有限集，通常是$n$位二进制字符串集，用$\lbrace0,1\rbrace^n$表示，例如$\lbrace0,1\rbrace^2=\lbrace 00,01,10,11 \rbrace$

定义：全集$U$上的概率分布是函数$P:U\rightarrow[0,1]$，该函数为全集中的每个元素分配一个0到1之间的数，该数称为该元素在全集中的权重或概率，$\sum_{x\in U}P(X)=1$

分布的例子：
- 均匀分布：各元素概率相等，即对于所有$x\in U$，$P(X)=1/|U|$
- 在$x_0$的点分布：取值为$x_0$时概率为1，其他时候概率为0

所有元素的概率分布都列出来可组成一个分布向量

定义：考虑全集的一个子集$A$，$Pr[A]=\sum_{x\in A}P(X)\in[0,1]$，子集A被称为**事件**。注意：$Pr[U]=1$

例：$U=\lbrace0,1\rbrace^8, A=\lbrace all\ x\ in\ U such\ that\ lsb_2(x)=11\rbrace\subseteq U$，$lsb_2$是最低两位的意思，这里A是最低两位是11的8位二进制数，若$U$是均匀分布，则$Pr[A]=0.25$

定义：联合边界：对于事件$A_1$和$A_2$，$Pr[A_1\cup A_2]\leq Pr[A_1]+Pr[A_2]$，当且仅当$A_1\cap A_2=\emptyset$时取等号。

例：$A_1=\lbrace all\ x\ in\ \lbrace 0,1\rbrace^n \ s.t.\ lsb_2(x)=11\rbrace, A_1=\lbrace all\ x\ in\ \lbrace 0,1\rbrace^n \ s.t.\ msb_2(x)=11\rbrace$

$Pr[lsb_2(x)=11\ or\ msb_2(x)=11]=Pr[A_1\cap A_2]\leq\frac{1}{4}+\frac{1}{4}=\frac{1}{2}$

定义：随机变量：一个随机变量$X$是一个从全集映射到某个集合V的函数$X:U\rightarrow V$，集合V被称为随机变量的值域。

例：$X:\lbrace0,1\rbrace^n\rightarrow\lbrace0,1\rbrace$; $X(y)=lsb(y)\in\lbrace0,1\rbrace$，对于该映射，当U是均匀分布时，可知$Pr[X=0]=Pr[X=1]=\frac{1}{2}$

更一般地，一个随机变量X从集合V中取值，则该随机变量实际上生成了集合V的概率分布：$Pr[X=v]:=Pr[X^{-1}(v)]$，含义：随机变量输出v的概率等同于从全集中随机抽取一个元素，对其施加函数X，然后判断其输出v的概率。换言之，我们X输出v的概率，就等于从全集中抽一个元素，正好落在v在函数X下的原像中。

定义：均匀随机变量：假定U是某有限集，例$\lbrace 0,1 \rbrace^n$，则用$r\stackrel{R}\leftarrow U$表示U上的均匀随机变量，对于所有$a\in U: Pr[r=a]=1/|U|$。这里$\stackrel{R}\leftarrow$表示均匀抽样。正式来说，$r$是恒等函数，对于所有$x\in U$而言，$r(x)=x$。

- 确定性算法：给定输入，总产生固定的输出：$y\leftarrow A(m)$
- 随机化算法：输入的除了给定的m，还有一个隐形参数r，每次执行算法时会重新抽取，因此每次输出会改变，$y\leftarrow A(m;r)\ where\ r\stackrel{R}\leftarrow \lbrace 0,1 \rbrace^n$，即$y\stackrel{R}\leftarrow A(m)$

例：用随机字符串k来加密输入的消息m，$A(m;k)=F(k,m), y\stackrel{R}\leftarrow A(m)$

定义：独立性：当$Pr[A\ and\ B]=Pr[A]\cdot Pr[B]$时，事件A和B独立。当$\forall a,b\in V:Pr[X=a\ and\ Y=b]=Pr[X=a]\cdot Pr[Y=b]$时，从集合V中取值的随机变量X和Y独立。

例：$U=\lbrace 0,1\rbrace^2=\lbrace 00,01,10,11 \rbrace$且$r\stackrel{R}\leftarrow U$，定义随机变量X和Y为$X=lsb(r),Y=msb(r)$，则$Pr[X=0\ and\ Y=0]=Pr[r=00]=\frac{1}{4}=Pr[X=0]\cdot Pr[Y=0]$

定义：异或XOR：两个$\lbrace 0,1\rbrace^n$上的字符串执行异或操作是指按位加并模2，例如$0110111\oplus 1011010=1101101$。

定理：异或的属性：Y是$\lbrace 0,1\rbrace^n$上的随机变量，X是$\lbrace 0,1\rbrace^n$上的独立均匀分布变量，则$Z:=Y\oplus X$是$\lbrace 0,1\rbrace^n$上的均匀变量。

证明（$n=1$）：$Pr[Y=0]=P_0,Pr[Y=1]=P_1, Pr[X=0]=Pr[X=1]=0.5$，联合分布如下：

| Y | X | Pr |
| ---- | ---- | ---- |
| 0 | 0 | $0.5P_0$ |
| 0 | 1 | $0.5P_0$ |
| 1 | 0 | $0.5P_1$ |
| 1 | 1 | $0.5P_1$ |
$Pr[Z=0]=Pr[(0,0)or(1,1)]=0.5P_0+0.5P_1=0.5$，类似地，$Pr[Z=1]=0.5$。

生日悖论：令$r_1,...,r_n\in U$表示独立同分布的随机变量，则当$n=1.2\times |U|^{\frac{1}{2}}$时$Pr[\exists i\neq j:r_i=r_j]\geq \frac{1}{2}$。

例：$U=\lbrace 0,1 \rbrace^{128}$，采样约$2^{64}$随机消息，则其中两个消息很有可能相同。

现实一些的例子：多少人聚在一起会有至少两个人的生日相同？一年365天，因此是$1.2\sqrt{365}\approx 23$

## Stream Ciphers 1: the one-time pad and stream ciphers
### Information Theoretic Security and The One Time Pad

### Stream Ciphers and Pseudo Random Generators


