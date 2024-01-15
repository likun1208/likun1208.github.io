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
8. 操作符：对于谓词$b$，如果b为真，则$1\lbrace b\rbrace\in\lbrace 0,1\rbrace$返回1，否则返回0。
9. 对于$n\lt l,x\in\mathbb{U}_N$，$extend_{n,l}(x)$返回x左侧附加$(l-n)$个0。
10. 对于$x\in\mathbb{U}_N$，$MSB_n(x)\in\lbrace 0,1\rbrace$表示 x 的最高有效位。
11. 秘密共享：对于$x\in\mathbb{U}_N$，秘密共享是对随机共享$x_0,x_1\in\mathbb{U}_N$的采样，使得$x=x_0+x_1\ mod\ N$成立，并用$share(x)$表示。当$P_0$持有$x_0$且$P_1$持有$x_1$时，我们用$reconstruct(x_b), b\in\lbrace 0,1\rbrace$表示交换共享并将其相加从而重构基础值的过程。

### 威胁模型
这项工作考虑了预处理模型中的标准二方安全计算 [8, 9, 14, 22, 37]，在安全机器学习领域也受到了大量关注 [32, 38, 41, 63, 64, 76]。也就是说，存在两个方$P_0$和$P_1$，其输入为$x_0$和$x_1$，希望计算公共函数$y = f(x_0, x_1)$，而不向对方透露任何超出函数输出y之外的信息。在预处理阶段独立于函数输入$x_0$和$x_1$，相关随机性被生成并提供给$P_0$和$P_1$。这种随机性可以通过多种方式生成 —— 可信第三方 [11, 14, 32, 38, 41, 63, 64, 76]，通用的2PC协议 [28, 78]，或者更高效的专用2PC协议 [24]。在这项工作中，我们考虑第一种方法。我们的所有协议都满足标准的模拟安全性定义 [17, 28, 47]，在半诚实的静态多项式时间(PPT)攻击者模型下提供安全性，该攻击者破坏$P_0$或$P_1$中的一个。

### FSS
函数秘密共享是一对算法`(Gen, Eval)`，Gen将函数g拆分为两个函数份额$(g_0,g_1)$，Eval则输入$b\in\lbrace 0,1\rbrace$、函数$g_0$和x，并返回$g_b(x)$。一个FSS方案的正确性要求$g_0(x)+ g_1(x)= g(x)$对于所有的$x$成立。安全性要求则是每个函数份额$g_b$会隐藏底层函数$g$。

定义1：一个两方FSS方案是一对算法`(Gen,Eval)`使得：
- $Gen(1^\lambda,\hat{g})$是一个PPT（概率多项式时间）密钥生成算法，它接受安全参数$1^\lambda$和函数g的描述$\hat{g}\in\lbrace 0,1\rbrace^*$，输出一对密钥$(k_0, k_1)$。我们假设$\hat{g}$明确地包含输入和输出群$\mathbb{G}^{in}, \mathbb{G}^{out}$的描述。
- $Eval(b,k_b,x)$是多项式时间评估算法，输入计算方索引$b\in\lbrace 0,1\rbrace$、$g_b:\mathbb{G}^{in}\rightarrow\mathbb{G}^{out}$定义的密钥$k_0$和$g_b$的输入$x\in\mathbb{G}^{in}$，输出$g_b(x)$的值$y_0\in\mathbb{G}^{out}$。

$(k_0, k_1)$被称为FSS密钥。用于存储一个FSS密钥所需要的比特数称为密钥大小。

### 2PC
考虑具有门$\lbrace g_i\rbrace_i$和线$\lbrace w_i\rbrace_i$的电路的安全计算，我们分两阶段描述使用了[14]中的FSS进行预处理的2PC协议。

离线阶段。对于每个线$w_i$，从适当的组中采样一个随机掩码$r_i$，然后，对于每个具有输入线$w_i$和输出线$w_j$的门$g$，为其偏移函数$g^{[r_i,r_j]}(x)=g(x-r_i)+r_j$生成一个FSS密钥，并为每一方提供一个密钥。对于属于`b`方电路的输入和输出线，该方还知道与这些线相关联的掩码。

在线阶段。对于b方拥有的值为$x_i$的每条输入线$w_i$，b方计算$\hat{x}_i=x_i+r_i$并发送给1-b方。现在，双方按拓扑顺序评估电路门。为了评估有着输入和输出线$w_i$和$w_j$的门，双方评估$\hat{x}_i$上相应的FSS密钥，以获得$\hat{x}_j=g^{[r_i,r_j]}(\hat{x}_i)=g(\hat{x}_i-r_i)+r_j=g(x_i)+r_j$。然后各方重建份额以获得掩码值$\hat{x}_j$。对于输出线，拥有线的一方减去相应的掩码即可得到最终的输出值。

协议结构和FSS协议的安全性。我们使用$(\hat{\cdot})$表示掩码值。考虑一个函数F和输入x能使得$y=F(x)$。针对F的协议记作$\Pi^F$，有两个阶段$Gen^F$和$Eval^F$。
- 前者在预处理阶段对输入和输出掩码$r^{in},r^{out}$执行，以生成可用于$P_0,P_1$的预处理材料或密钥。存储$\Pi^F$的密钥所需的位数称为密钥大小，用$keysize(\Pi^F)$表示。
- 后者是$P_0,P_1$于在线阶段在掩码输入$\hat{x}=x+r^{in}$和它们各自密钥上运行的协议。在该阶段结束时，$P_0,P_1$学习掩码输出值$\hat{y}=y+r^{out}$。

本文提出的所有协议都具有上述结构。

安全性通过以下两个交互定义：
- 真实交互。$Gen^F$在预处理阶段执行（输入和输出掩码为$r^{in},r^{out}$），$P_0,P_1$使用预处理阶段获取的密钥于在现阶段执行$Eval^F$。该交互发生在存在对手A和环节Z的情况下。
- 理想交互。$P_0,P_1$发送输入到能诚实计算功能的功能（即，去掩码$\hat{x}$以得到`x`，计算$y=F(x)$，计算$\hat{y}=y+r^{out}$并提供$\hat{y}$的份额给$P_0,P_1$）。

如果对于真实交互中的每个对手A，在理想交互中都存在对手S（称为模拟器），使得没有环境Z能区分这两种交互，我们就说协议$\Pi^F$安全地实现了功能F。

### DPF
点函数$f_{\alpha,\beta}^{\bullet}:\mathbb{U}_N\rightarrow \mathbb{G}^{out}$输入$x\in\mathbb{U}_N$，如果$x=\alpha$则输出$\beta\in\mathbb{G}^{out}$，反之则输出0。点函数对应的FSS方案被称为分布点函数。

$(k_0^{\bullet},k_1^{\bullet})\leftarrow Gen_n^{\bullet}(1^\lambda,\alpha,\beta,\mathbb{G}^{out})$

$y_b=Eval_n^{\bullet}(b,k_b^{\bullet},x),x\in\mathbb{U}_N$

对于所有协议，取$\mathbb{G}^{out}=\lbrace 0,1\rbrace,\beta=1$就够了，这使得我们可以利用带有提前终止优化的分布式点函数构造(这适用于小负载)。

定理1（DPF的成本）给定PRG $G:\lbrace 0,1\rbrace^\lambda\rightarrow\lbrace0,1\rbrace^{2\lambda+2}$并令$v=\log_2(\lambda+1)$。当$n\gt v$，存在一个$f_{\alpha,1}^{\bullet}$的DPF：$\mathbb{U}_N\rightarrow\lbrace 0,1\rbrace$，其密钥大小是$(n-v)\cdot(\lambda+2)+2\lambda$。在$Gen_n^{\bullet}$中PRG的调用次数是$2(n-v)$，在$Eval_n^{\bullet}$中的用次数是$n-v$。当$n\leq v$时，需要$2^n$的keysize以及$Gen_n^{\bullet},Eval_n^{\bullet}$PRG调用次数为0。

与其他FSS的工作类似，我们设$\lambda=127$，并在计数器模式下使用两次AES-128调用来实现所需的长度加倍 PRG。正如之前所观察到的，单个 AES 调用足以满足$Eval_n^{\bullet}$，因为仅使用了一半的输出。从现在开始，我们将其称为半 PRG 调用。

### 使用DPF密钥比较
比较函数：

## Transformer概要

