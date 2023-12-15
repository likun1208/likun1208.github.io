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
|$F_{Mult}$|密钥共享乘法的理想函数：将$g$位整数与$h$位整数相乘得到$l=(g+h)$位输出，且无溢出|
|$F_{CMP}$|比较$b\leftarrow CMP(x,y)$的理想函数：如果$x\geq y$，则$b=1$，反之则$b=0$|
|$F_{MUX}$|多路复用器$y\leftarrow MUX(x,b)$的理想函数：如果$b=1$，则$y=x$，如果$b=0$，则$y=0$|
|$F_{Trunc}$|截断$y\leftarrow Trunc(x,s)$的理想函数：对于$x,y\in\mathbb{Z}_{2^l}$，$y=x\gg s$|
|$F_{TR}$|截断后减少$y\leftarrow TR(x,s)$的理想函数：对于$x\in\mathbb{Z}_{2^l}$和$y\in \mathbb{Z}_{2^{l-s}}$，$y=x\gg s$|
|$F_{LUT}$|查找表$T[i]\leftarrow LUT(T,i)$的理想函数|
|$F_{Shuffle}$|洗牌的理想函数|
|$\alpha$|$y:=GELU(x)$的分割：当$x<-\alpha$时$y:=0$；当$-\alpha\leq x \leq \alpha$时$y:=GELU(x)$；当$x>\alpha$时$y:=x$|
|$s$|$2^s$是在$[\\alpha,\alpha]$内的间隔数的数量|
|$(a_i,d_i)$|$y=a_ix+d_i$是每个间隔内逼近$GELU(x)$的线性函数|
|$g$|$a_i$的位长度|
|$t$|响应词（输入矩阵）|
|$N$|FHE中的多项式模度|
|$M$|注意力头|
|$T$|温度|
### 密码原语



## 安全矩阵乘法

## 安全GELU

## 安全前K项选取

## 安全采样

## CipherGPT框架

## 实验评估