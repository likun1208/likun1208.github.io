---
title: '论文记录-An Efficient Anonymous Authentication and Reputation Management Scheme for Mobile Crowdsensing in Vehicular Networks'
date: 2021-10-19 10:08:36
tags: crowdsourcing
categories: 论文
description: 是导师安排的审稿任务
---

# 车联网中的高效匿名认证与信誉管理框架

这篇是投稿到[IEEE Transactions on Vehicular Technology](mailto:onbehalfof@manuscriptcentral.com)的论文，现在要写审稿意见。

作者：三个暨南大学，一个澳门城市大学

## 摘要

车联网的众包系统中，匿名认证和信誉管理很重要，也面临很多问题。本文提出了一个既提高参与者相互认证效率，又实现传感车辆声誉管理的新方案。具体来说，匿名身份验证机制用于实现数据请求者、云服务器和传感车辆之间的相互认证，信誉评分更新方法将评分转化为模糊的信誉评级，以提高不可连接性（？）。传感数据使用Paillier算法保护。模拟实验中分析了隐私、计算和通信成本，表明效率优于现有方案。

## Introduction

MCS（移动群智感知）越来越重要，车联网中的实时传感提升了传感结果精度；但是由于数据上传和匿名认证过程中的庞大、开放、稀疏、高度动态性的特点，车联网在面对攻击时是脆弱的；此外，多个传感器同时参与传感任务会增加计算和通信的负担。因此，如何获取高质量数据和实现高效匿名认证是亟待解决的问题。信誉评分系统可以用于构建可信车联网，但因为传感车辆的轨迹和驾驶模式可以通过接入信誉评分来推断，仅仅使用这个系统是不够的。信誉评分系统和匿名认证过程中面临的隐私问题直接影响传感车辆接入车联网的意愿和采集数据的精度。综上，本文提出了一个新方案，主要贡献如下：

1. 通过采用定期更新和模糊化方法，实现了假名和信誉评分的不可链接性。
2. 提出了一种有效的匿名认证方法，根据该方法，可以减少计算和通信费用，提高身份验证效率。
3. 通过分发诚实传感车辆的高信誉分数和恶意车辆的低声誉分数，实现了高效、实时的声誉管理。
4. 对隐私和安全性进行了理论分析，模拟实验表明本文方法的计算和通信开销以及信誉更新算法的性能优于现有方案。

## Related Word

## Preliminaries

### Bilinear pairing（双线性配对）

对称加密技术。$G_1$、$G_2$、$G_T$是三个素数$p$阶群乘法循环群，双线性配对是有这下列属性的$\hat{e}:G_1\times G_2 \rightarrow G_T$：

1. 双线性：对于所有$g\in G_1, h\in G_2, a,b \in Z_p,\hat{e}(g^a,h^b)=\hat{e}(g,h)^{ab}$
2. 非退化性：对于所有$g\neq1_{G_1},h\neq1_{G_2},\hat{e}(g,h)\neq 1_{G_T}$
3. 可计算性：$\hat{e}$是可计算的

### Beta distribution

Beta分布是定义在区间`(0,1)`的连续概率分布，两个参数分别是$\alpha$和$beta$。随机变量X服从Beta分布记作$X~Beta(\alpha,\beta)$，其概率密度函数$f(x|\alpha,\beta)$是Gamma函数的函数：
$$
f(x|\alpha,\beta)=\frac{\Gamma(\alpha+\beta)}{\Gamma(\alpha)\Gamma(\beta)}x^{\alpha-1}(1-x)^{\beta-1}
$$
其中，
$$
\Gamma(\alpha)=\int_0^\infty x^{\alpha-1}e^{-x}dx,0\leq x\leq 1,\alpha>0,\beta>0
$$
Beta分布的数学期望$E(x)=\frac{\alpha}{\alpha+\beta}$。

### Paillier algorithm

是同态加密算法，内容如下：

1. 初始化：选择两个大质数$p$和$q$，计算$n=p\cdot q$
2. 密钥生成：计算$\delta = LCM(p-1,q-1)$，选择随机数$g_a\in Z_{n^2}^*$，计算非公开参数$\eta=(L(g_\alpha^\delta\ \ mod\ \ n^2))^{-1}mod\ \ n$，其中L是$L(x)=(x-1)/n$。公钥和私钥分别是$P_k=(n,g_a)$和$S_k=(\delta,\eta)$
3. 加密：选择随机数$r\in Z_{n^2}^*$，对信息$m$使用公钥$P_k$加密，得到密文C：$C=g_a^m\cdot r_i^n\ \ mod\ \ n$
4. 解密：使用密钥对密文C解密，得到信息m：$m=L(C^\delta\ \ mod\ \ n^2)\cdot \eta\ \ mod\ \ n$

## Problem Statement

### System architecture

1. 可信认证（TA）：完全被信任，有强大算力，具体来说，TA 负责系统参数设置、实体注册以及车辆网络中的恶意 SV 跟踪和撤销。此外，TA 还分发和更新 SV 的信誉评分和身份证明。此外，它存储其他实体的注册信息，并保存其私钥，其公钥为 SV 所知。
2. 数据请求者：分配传感任务
3. 云服务器：有强大的存储空间和算力，收集和处理来自SV的密文，对SV的传感数据进行评分并反馈给TA
4. 传感车辆（SV）：装备多种传感器、可互相通信的实体

匿名认证和信誉管理系统步骤如下：

1. 实体注册：数据请求者、云服务器和SV在TA进行注册从而参与传感任务
2. 任务分配：数据请求者在云服务器上分配传感任务，云服务器向SV广播
3. 车辆选择：注册后，每个SV向云服务器发送认证信息，云服务器根据信誉评分和身份认证选择SV
4. 数据收集：SV收集数据并加密发送到云服务器
5. 数据处理：云服务器验证并聚合密文
6. 结果提交：云服务器把聚合密文交给数据请求者
7. 反馈报告：云服务器生成所有参与传感任务的SV的信誉反馈报告并发送给TA
8. 更新评分和认证：TA更新SV的信誉评分和身份认证，并将其发送给相应的SV

### Security model

TA完全被信任；数据请求者和云服务器诚实却对SV的隐私感到好奇；SV对其他SV的传感数据感到好奇，

信誉/假名攻击：当SV上传数据时，攻击者可能会链入信誉评分系统和假名进行攻击

中间人攻击：恶意SV会窃取认证信息欺骗云服务器并提供假数据

女巫攻击：恶意SV会建立多个实体从而传输多份假数据

替换攻击：恶意SV会用错误数据替换正确数据从而通过数据评估

### Design goals

隐私目标：

1. 认证隐私：SV的真实身份不会暴露，假名不会被恶意攻击链接
2. 数据隐私：传感数据受保护，除数据请求者以外的其他实体无法获取

安全目标：防止前面提到的四种攻击

## The Design Of Our Scheme

### System initialization

1. TA初始化：TA首先运行系统生成算法`Gen(k)`生成系统参数$\{p,q,P_1,P_2,G_1,G_2,G_T,\hat{e}\}$，该算法中的`k`是安全参数，$G_1,G_2,G_T$是三个$p$阶循环群，$\hat{e}$是双线性映射，$P_1\in G_1,P_2\in G_2$. 接下来TA计算$G=\hat{e}(P_1,P_2)$，定义一个非对称加密算法`Ene()`和一个加密哈希函数$H(0,1)^*\rightarrow Z_n^*$，其中$n=p\cdot q$，此外，TA初始化密码对$(Sk_T,Pk_T)(Sk_T\in Z_n^*,Pk_T=Sk_t\cdot P_1)$，并计算认证参数$A=\frac{1}{Sk_T}\cdot P_2$. 最后，TA将$(p,q,Sk_T)$作为隐私，公开$(n,P_1,P_2,G_1,G_2,G_T,\hat{e},Pk_T,H,Enc(),A)$
2. 云服务器注册：

### Sensing task assignment

### Sensing vehicle selection

### Data collection and processing

### Reputation and certificate updating

### Sensing vehicle revocation

## Analysis

## Performance evaluation

## Conclusion

## 参考

1. [双线性配对](https://www.zhihu.com/question/39641890)
