---
title: "论文记录-SIGMA: Secure GPT Inference with Function Secret Sharing"
date: 2023-01-08 16:45:50
tags:
  - LLM
  - Privacy
  - Security
  - MPC
categories: 论文
description: LLM和MPC结合的论文之一
---
# SIGMA: Secure GPT Inference with Function Secret Sharing

## Abstract
- 安全两方计算
- 现有的安全推理解决方案面临着高延迟和通信开销的问题，尤其是transformer
- 函数秘密共享（FSS）是一种通过预处理阶段获得高效 2PC 协议的最新范例
- 本文：SIGMA——第一个基于 FSS 的安全transformer推理端到端系统
	- 通过为复杂的机器学习功能（例如 Softmax 和 GeLU）构建新的基于 FSS 的协议，并加速它们在 GPU 上的计算，SIGMA 将 Transformer 安全推理的延迟比使用现有技术的现有技术提高了 11 − 19 倍预处理和 GPU。
	- 我们提出了生成式预训练 Transformer (GPT) 模型的第一个安全推理。特别是，SIGMA 在 7.4 秒内执行了 13 亿个参数的 GPT-Neo，在 1.6 秒内执行了 HuggingFace 的 GPT2。
## Intro
对安全推理问题的定义：
- 模型提供商拥有专有的机器学习 (ML) 模型，将其作为服务提供
- 客户希望了解其敏感数据的推理结果
- 安全要求是除了推理输出之外，客户端不应了解任何有关模型的信息，并且模型提供者不应了解有关客户端输入的任何信息

2PC的发展：
1. 具有数千个参数的模型扩展到[10]、[39]、[48]、[50]–[52]、[55]、[58]、[60] ]、[62]、[72]
2. 具有数百万个参数的模型 [20]、[32]、[35]、[38]、[42]、[59]、[73]、[76]
3. 到目前为止具有数亿个参数的 BERT 模型 [7]、[19]、[33]、[41]、[45]
4. 本文：提供具有数十亿参数的生成预训练 Transformer (GPT) 模型的安全推理

用于安全机器学习推理的系统必须满足以下要求：
1. 准确性：即安全推理下的准确性应与明文的准确性相匹配
2. 安全性：即系统应提供标准的2PC安全性
3. 效率：即，安全推理的延迟和通信开销应该很低
4. 可扩展性：即，系统必须扩展到具有数十亿个参数的模型。

这里列了几个安全transformer推理模型和缺点，survey中可以对比。

本文贡献：和CipherGPT差不多，也是针对各操作设计了安全计算协议，包括矩阵乘法、激活函数等。

## 预备知识
### 符号
1. $\lambda$：计算安全参数
2. $N=2^n,L=2^l$
3. $\mathbb{R}$：实数集，$\mathbb{U}_{2^n}$：n位无符号整数集
4. 用2补数表示$\mathbb{U}_{N}$中的有符号数
5. 对于$x\in\mathbb{U}_N$，$int_n(x),uint_n(x)$分别表示$\mathbb{Z}$中的有符号整数和无符号整数
6. 粗体表示数字，普通字体带$[i]$表示第i个元素，如$\mathbf{a}=\lbrace a[0],a[1],a[2]... \rbrace$
7. 定点数表示法：由位宽n和精度f参数化，将实数$r\in\mathbb{R}$编码为一个n位整数$x\in\mathbb{U}_N$使得$x=\lfloor r\cdot 2f \rfloor/\ mod\ N$。反过来，精度为f的n位定点数x解码为一个实数$\frac{int_n(x)}{2^f}$
8. 操作符：对于谓词$b$，$1\lbrace b\rbrace$


### 威胁模型

### FSS

### 2PC

### DPF

## Transformer概要

