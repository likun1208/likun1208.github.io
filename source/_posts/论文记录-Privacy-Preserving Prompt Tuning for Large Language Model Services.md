---
title: 论文记录-Privacy-Preserving Prompt Tuning for Large Language Model Services
date: 2023-11-24 11:07:40
tags:
  - LLM
  - Privacy
  - Security
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
	3. 由于直接在私有化数据上训练时提示调整表现不佳，因此我们引入了一种新颖的私有化token重建任务，该任务与下游任务联合训练，使LLM能够学习更好的依赖于任务的表示。

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

6. 为了应用文本到文本的隐私化，需要一个嵌入模型把单词映射到向量。给定序列$x=[x_1,...,x_n]$和嵌入模型$E\in \mathbb{R}^{|V|d}$，其中$|V|$和$d$是词汇大小和嵌入维度。嵌入模型将$x$映射到向量$[x_1,...,x_n]$的序列。
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
$f$表示LLM模型的主干网络。一个长度为N的提示词$P\in\mathbb{R}^{N\times h}$是连续向量$[p_1,...,p_N]$中的序列，其中$p_i\in\mathbb{R}^h$是可调整的虚拟token嵌入，$h$是主干网络的隐藏层维度。给定输入序列$\mathrm{x}=[x_i,...,x_n]$，提示词被添加到$X\in\mathbb{R}^{n\times h}$之前，其中$X=[x_1,...,x_n]$是词嵌入的序列，$x\in\mathbb{R}^h$。由此可以通过使用LLM得到激活序列：
$$
H=f([\![P;X]\!])\tag{4}
$$
其中$[\![P;X]\!]$表示两个序列P和X的串联，$H\in\mathbb{R}^{n\times h}$是激活序列。H用于预测任务依赖的标签，通常通过语言模型的head。

#### Privatized Token Reconstruction
1. 直接在隐私化的数据上进行提示词调优会显著降低LLM在下游任务上的性能，说明提示词调优对这种文本到文本的隐私化带来的随机扰动很敏感。
2. 一些研究发现掩码语言模型目标有助于学习可分离的深度表示。
3. 受此启发本文提出了隐私化token重建任务，与下游任务联合训练，提高LLM在隐私保护的下游任务上的性能。
4. token重建类似于掩码语言模型，为了保护隐私不能直接重建单词，但是可以在训练数据中的每个输入前添加一系列token（称为普通token），它由任意选择的token组成，可以安全发送而不会引起隐私泄露。
5. 普通token记作$k=[k_1,...,k_m]$
6. 训练例子记作$\mathcal{D}=\{\left \langle x_i,y_i \right \rangle|i=1,...,|\mathcal{D}|\}$，其中$|\mathcal{D}|$表示训练数据的尺寸
7. 用户首先在每个输入$x_i$的前面加入$k$，然后进行加密得到隐私化的版本$z=M([\![k;x_i]\!])=[k'_1,...,k'_m,x'_1,...,x'_n]$
8. LLM使用提示P和输入z生成激活序列G：
$$
G=f([\![P;Z]\!])\tag{5}
$$
其中Z是z的词嵌入序列，$G\in\mathbb{R}^{(m+n)\times h}$
9. 得到G后，我们使用其中的前m个向量$G_{1:m}=[g_1,...,g_m]$重建普通token，为了实现这一过程，我们引入了一个附加的重建head，由两个线性层组成。预测第$i$个token在普通token里的概率分布是：
$$
p_i=softmax(W_1W_2g_i)\tag{6}
$$
其中$W_1\in \mathbb{R}^{|T|\times c},W_2\in\mathbb{R}^{c\times h}$，都是重建head的参数，c是重建head的隐藏层的尺寸，$|T|$表示重建head的词汇量。隐私化token重建任务的损失函数是
$$
\mathcal{L}_{rec}=-\sum_{i=1}^m log p_i[j_i]\tag{7}
$$
其中，$j_i$是$k_i$在重建head词汇表中的索引，$p_i[j_i]$是向量$p_i$中的第$j_i$个标量。重建head的词汇表和LLM的词汇表不需要一致。
10.  剩下的n个激活函数$G_{n:m+n}$被用于预测任务相关的标签。假设下游任务使用交叉熵损失，$y_i\in\{0,1\}^{|C|}$是关联标签，$y_j$表示第j类标签，$|C|$是类别数量。
11. 下游任务的目标函数是：
$$
p=g(G_{m:m+n})\tag{8}
$$
$$
\mathcal{L}_{task}=-\sum_{i=1}^{|C|}y_ilogp[i]\tag{9}
$$
其中g是语言模型head的函数。
12. 由此，训练RAPT的损失函数是：
$$
\mathcal{L}=\mathcal{L}_{task}+\mathcal{L}_{rec}\tag{10}
$$
13. 在推理阶段，用户端也会应用文本到文本私有化来保护隐私信息，并从 LLM 服务中获取预测结果。我们注意到，推理阶段不使用重建head，因此$\{W_1, W_2\}$可以在训练后丢弃。

## 总结
1. 这篇文章主要的贡献是做了token重建，帮助LLM学习可分离的深度表示，提升了LLM在加密文本数据上的性能，而加密过程则是直接用了已有的本地差分隐私算法。
2. 加了一个普通token为什么能提升性能这一点我还没理解。