---
title: "论文记录-CipherGPT: Secure Two-Party GPT Inference"
date: 2023-12-08 15:22:40
tags:
  - LLM
  - Privacy
  - Security
  - MPC
categories: 论文
description: LLM和MPC结合的论文之一
---
# CipherGPT: Secure Two-Party GPT Inference

## Abstract
1. GPT面临隐私问题；
2. 由于数据量大、激活函数复杂，现有的安全方案不支持GPT；
3. 本文：CipherGPT——第一个安全两方GPT框架
	1. 提出了一种专为 GPT 推理定制的安全矩阵乘法，与 SOTA 相比，速度提高了 2.5 倍，带宽减少了 11.2 倍
	2. 提出了一种用于安全计算 GELU 的新协议，在运行时间、通信量和精度方面分别比 SOTA 提高了 4.2 倍、3.4 倍和 10.9 倍
	3. 提出了首个 top-k 采样协议

## Intro
1. 介绍ChatGPT及其面临的隐私问题
2. 安全推理：一种两方加密协议，运行推理阶段的方式如下：
	1. 服务器 (S) 对客户端的输入一无所知，而客户端 (C) 除了推理结果之外对模型一无所知。
	2. 协议通过让 S 和 C 运用同态加密和秘密共享等定制加密技术在加密输入上运行加密模型来进行。
	3. 通常引入预处理阶段来准备一些昂贵且与输入无关的工作，以便在线阶段可以高效地完成。
3. 现有安全推理应用到GPT的问题：
	1. 一些技术只针对某种网络结构，而不支持GPT；
	2. LLM需要大量高维矩阵乘法和复杂的数学函数（例如 GELU）。
4. 本文贡献：CipherGPT
	1. 基于 VOLE 的矩阵乘法
	2. 基于样条的GELU
	3. 基于洗牌的前K项选择
	4. 安全采样

## 预备知识
### 安全推理和威胁模型
安全推理是一种两方加密协议，有助于客户端 C 和服务器 S 之间的模型推理。该协议确保 C 只获得有关模型架构和推理结果的知识，同时隐藏 S 模型的所有其他细节。类似地，S 仍然不知道 C 的输入以及推理的输出。在 GPT 推理的上下文中，C 的输入是提示，S 的模型包含 Transformer 解码器的多次迭代以及 vec2word 层。

我们假设 C 或 S 可以是半诚实对手，它遵循协议规范，但尝试在协议执行期间收集尽可能多的信息。我们假设对手在计算上是有限的，并且使用$\lambda$来表示计算安全参数。

| 符号 | 含义 |
|:---:|:---|
|$C$|客户端|
|$S$|服务器|
|$n$|每层输入向量的长度|
|$L$|初始输入左移位|
|$l$|输入左移后的位长|
|${\langle x\rangle}^l$|$(\langle x \rangle_S^l,\langle x \rangle_C^l)$使得$x=\langle x \rangle_S^l+\langle x \rangle_C^l$ mod $2^l$|
|$F_{Mult}$|密钥共享乘法的理想功能：将$g$位整数与$h$位整数相乘得到$l=(g+h)$位输出，且无溢出|
|$F_{CMP}$|比较$b\leftarrow CMP(x,y)$的理想功能：如果$x\geq y$，则$b=1$，反之则$b=0$|
|$F_{MUX}$|多路复用器$y\leftarrow MUX(x,b)$的理想功能：如果$b=1$，则$y=x$，如果$b=0$，则$y=0$|
|$F_{Trunc}$|截断$y\leftarrow Trunc(x,s)$的理想功能：对于$x,y\in\mathbb{Z}_{2^l}$，$y=x\gg s$|
|$F_{TR}$|截断后减少$y\leftarrow TR(x,s)$的理想功能：对于$x\in\mathbb{Z}_{2^l}$和$y\in \mathbb{Z}_{2^{l-s}}$，$y=x\gg s$|
|$F_{LUT}$|查找表$T[i]\leftarrow LUT(T,i)$的理想功能|
|$F_{Shuffle}$|洗牌的理想功能|
|$\alpha$|$y:=GELU(x)$的分割：当$x<-\alpha$时$y:=0$；当$-\alpha\leq x \leq \alpha$时$y:=GELU(x)$；当$x>\alpha$时$y:=x$|
|$s$|$2^s$是在$[\\alpha,\alpha]$内的间隔数的数量|
|$(a_i,d_i)$|$y=a_ix+d_i$是每个间隔内逼近$GELU(x)$的线性函数|
|$g$|$a_i$的位长度|
|$t$|响应词（输入矩阵）|
|$N$|FHE中的多项式模度|
|$M$|注意力头|
|$T$|温度|

### 密码学原语
1. 秘密共享：我们在不同的2次幂环上使用2-2附加秘密共享方案。对于$x\in\mathbb{Z}_{2^t}$，我们用$\lt x \gt^l=(\lt x \gt^l_S,\lt x \gt^l_C)s.t.x=\lt x \gt^l_S+\lt x \gt^l_C mod 2^l$表示其shares。为了简化表达，当上下文无关时我们省略上标的$l$。
2. 非均匀位宽乘法：理想功能$F_{Mult}$以$\lt x \gt^g$和$\lt y \gt^h$为输入，返回$\lt z \gt^l$，其中$z=xy$且$l=g+h$。一个实现该功能的简单方法是首先将输入都扩展到$l$位，然后使用标准协议进行统一位宽的秘密共享乘法。SIRNN提供了一个协议，其性能比这个简单的解决方啊高1.5倍。该协议的通信复杂度是$\mu(\lambda+\mu/2+1/2)+mn$，其中$\mu=min(m,n)$。
3. 安全比较：理想功能$F_{CMP}$以$\lt x \gt^l$和$\lt y \gt^l$为输入，返回$\lt b \gt^l$，其中若$x\geq y$则$b=1$，否则$b=0$。$CrypTFlow2$为$F_{CMP}$提供了一个有效的协议，其通信成本小于$\lambda l+14l$位和$\log l$轮。
4. 安全多路复用器：理想功能$F_{MUX}$以$\lt x \gt^l$和$\lt b \gt^l$为输入，返回$\lt y \gt^l$，其中如果$b=1$则$y=x$，如果$b=0$则$y=0$。本文采用的安全多路复用器是SIRNN提出的，通信要求$2(\lambda+l)$位。
5. 安全截断：理想功能$F_{Trunc}$以$\lt x \gt^l$和$s$为输入，返回$\lt y \gt^l$，其中$y=x\gg s$。SIRNN提供了安全截断的协议，通信要求小于$\lambda(l+3)+15l+s+20$位和$\log l+3$轮。
6. 截断后减小：理想功能$F_{TR}$以$\lt x \gt^l$和$s$为输入，返回$\lt y \gt^{l-s}$，其中$y=x\gg s$。需要注意，这个和前一个的区别在于这个会通过右移操作将输出减少到一个更小的环上，而前一个则将输出保留在原始环上。SIRNN提供了协议，通信成本小于$\lambda(s+1)+l+13s$位。
7. 查表：理想功能$F_{LUT}$以$\lt i \gt$为输入，返回$\lt T[i] \gt$，其中$T$是有M个条目的表。该函数通过对$\tbinom{M}{1}-OT$的单次调用来实现。更有效的解决方案是先将LUT描述转换为布尔表达式，然后使用多扇入内积对其进行评估。该协议在预处理阶段需要$(mt+4)(2^M-M-1)$位通信，在在线阶段需要$2\sigma$位。
8. 秘密共享洗牌：理想功能$F_{Shuffle}$以$\lt \mathbf{x} \gt$和$\lt\pi\gt$为输入，返回$\lt\pi(\mathbf{x})\gt$，其中$\pi$是排列函数。Chase等提出了使用OT和PRG等轻量级原语来实现该功能的高效构造。该方法涉及使用PRF来构建排列共享协议，该协议允许两方使用一方选择的排列来排列输入向量。这个排列和共享协议运行两次，每方各选一次。为了混洗$n$个$l$位元素，该协议的通信成本与$\lambda n\log n+nl\log n/\log T$成正比，计算成本位$(nT\log n/\log T)(l/\lambda)$对称密钥操作，其中$T$在16和256之间。
9. 子域向量遗忘线性评估：VOLE是一个两方功能，它从发送方获取标量$x\in\mathbb{F}_p$并生成 VOLE 相关性：
$$
w = ux + v \tag{1}
$$
接收方学习$(\mathbf{u},\mathbf{v})\in_R \mathbb{F}_p^n\times\mathbb{F}_p^n$，发送方学习$w\in_R\mathbb{F}_p^n$。VOLE 通常基于带噪声奇偶校验（LPN）和可标点 PRFs 来构建，因此其复杂性几乎与$n$无关。子域VOLE(sVOLE)是VOLE的广义化，其中$\mathbf{u}\in_R\mathbb{F}_p^n,x\in\mathbb{F}_q,\mathbf{w,v}\in_R\mathbb{F}_q^n,q=p^m$。sVOLE 实现的任务与运行 m 个普通 VOLE 实例相同，但成本更低。VOLE 和 sVOLE 最初是针对有限域提出的。Baum 等人提出了在有限环（如$\mathbb{Z}_{2^l}$）上运行的方法。
10. 同态加密：全同态加密（FHE）是一种允许对加密数据进行任意操作的加密方案 。在实践中，它通常以分级的方式使用：操作只能在有限的时间内进行，否则密文无法解密。在大多数 FHE 密码系统中，明文被编码为来自商环$\mathbb{Z}_p[x]/(x^N +1)$的多项式，其中$N$是 2 的幂次，$p$是明文的模数。然后将明文多项式加密为密文多项式$\mathbb{Z}_q[x]/(x^N+1)$，其中$q$是密文模数，它决定了密文的安全级别以及可执行操作的次数。

## 安全矩阵乘法

## 安全GELU

## 安全前K项选取

## 安全采样

## CipherGPT框架

## 实验评估