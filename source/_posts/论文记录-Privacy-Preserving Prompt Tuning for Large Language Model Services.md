---
title: 论文记录-Privacy-Preserving Prompt Tuning for Large Language Model Services
date: 2023-11-24 11:07:40
tags:
  - LLM
  - Privacy
categories: 论文
description: LLM Prompt的隐私保护
---
# Privacy-Preserving Prompt Tuning for Large Language Model Services
## 背景
1. LLM的Prompt会泄露隐私
2. 现有方法基本分为两类：
	1. 中心化隐私保护：找个服务商解决问题，但是服务商也可能会泄露
	2. 本地隐私保护：本地处理文本使其尽可能不泄露信息，但是会降低模型效果
3. 本文：
	1. 基于即时调优，我们提出了隐私保护即时调优（RAPT），这是一个为LLM服务提供隐私保证的框架。 
	2. RAPT采用本地隐私设置，允许用户通过本地差分隐私在本地对其数据进行私有化。
	3. 由于直接在私有化数据上训练时提示调整表现不佳，因此我们引入了一种新颖的私有化令牌重建任务，该任务与下游任务联合训练，使LLM能够学习更好的依赖于任务的表示。

## 方法
首先引入文本到文本私有化，它用于用户在本地私有化他们的数据。

然后，我们描述如何使用我们的提示方法定制和利用带有私有化数据的LLM服务。

### Text-to-Text Privatization
1. 本地加密可以避免窃听攻击等。
2. 大多数LLM采用文本到文本的推断，因此本文采用文本到文本的隐私保护。
3. 文本到文本的隐私保护基于$d_{\mathcal{X}}$隐私，是局部差分隐私的基于距离的松弛形式，广泛用于保护文本内容的隐私。
4. 对于给定输入集$\mathcal{X}$和输出集$\mathcal{Y}$，$d_{\mathcal{X}}$是一个定义在$\mathcal{X}$上的距离函数。
5. 随机机制$M:\mathcal{X}\rightarrow\mathcal{Y}$满足$d_{\mathcal{X}}$隐私，当且仅当，对于任意$x\in\mathcal{X}$且$x'\in\mathcal{X}$，输出$M(x)$和$M(x')$的分布受限于：
$$
\frac{P(M(x)=y)}{P\left(M\left(x^{\prime}\right)=y\right)} \leq e^{\eta d_{\mathcal{X}}\left(x, x^{\prime}\right)}, \quad \forall \boldsymbol{y} \in \mathcal{Y}\tag{1}
$$
其中，$\eta\geq 0$是隐私参数，控制了隐私保护程度。

6. 为了应用文本到文本的隐私化，受限需要一个嵌入模型把单词映射到向量。给定序列$x=[x_1,...,x_n]$和嵌入模型$E\in \mathbb{R}^{|V|d}$，其中$|V|$和$d$是词汇大小和嵌入维度。嵌入模型将$x$映射到向量$[x_1,...,x_n]$的序列。
7. 假设使用$L_2$距离作为距离函数，将隐私参数为$\eta$的$d_{\mathcal{X}}$应用于一个词嵌入$x_t\in\mathbb{R}^d$可以通过给$x_t$添加随机噪声$z\sim exp(-\eta \|z\|)$来实现。令$z=lv$，采样$z$就等价于先采样标量$l\sim \Gamma(d,\frac{1}{\eta})$再从单位球空间$\mathbb{B}^d$中均匀地采样向量$v$。因此，$x_t$的隐私化表达$M(x_t)$可以形式化地表达为
$$
M(x_t)=x_t+z\tag{2}
$$
8. 接下来我们将$x_t$替换为嵌入空间中最接近$x_t$的单词$x'_t$：
$$
x'_t=argmin_k\|w_k-M(w_t)\|\tag{3}
$$
通过重复替换序列$x$中的单词，我们获得$x$的隐私化文本$M(x)$。

### LLM Customization
整体场景：假设用户通过及时调整API来定制LLM服务，将其私有化培训数据上传给服务提供商。

#### Prompt Tuning

#### Privatized Token Reconstruction
