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
