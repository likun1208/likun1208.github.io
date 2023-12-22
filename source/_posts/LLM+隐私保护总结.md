---
title: LLM+隐私保护总结
date: 2023-12-6 10:21:00
tags:
  - LLM
  - Privacy
  - Security
categories: slides
slidehtml: true
---
# LLM+隐私保护总结

<!-- Slide Start --> 

<style type="text/css">
  .reveal p {
    text-align: left;
  }
  .reveal ul {
    display: block;
  }
  .reveal ol {
    display: block;
  }
  .reveal {  
	max-width: 100%;
	max-height: 100vh;  
  }
  .slides {
	width: 100% !important;
	height: 0 !important; 
	padding-bottom: 56.25% !important;
  }
  .slides section {
	padding: 0 !important;
  }
</style>

2023年12月6日

---
### 当前LMM常见的隐私攻击
1. 成员推理攻击：确定训练中是否包含了某个用户的数据。
2. 模型反演攻击：近似重建训练数据。
3. 训练数据提取攻击：旨在准确揭示训练样本，是最强大的攻击，对用户造成的后果也最严重。

### LMM会被攻击的原因
1. LMM训练时用到了来自各渠道的大量数据
2. LMM具有记忆数据的特性，容易记住一些可能有隐私风险的训练数据

---
### 文献分类
1. 训练阶段处理数据
2. 预测阶段添加扰动
3. 知识遗忘
4. 风险检测

---
### 训练阶段处理数据
1. 去除重复数据
2. 结合差分隐私（最普遍）

--
#### 去除重复数据
<p>Carlini N, Tramer F, Wallace E, et al. Extracting training data from large language models[C]//30th USENIX Security Symposium (USENIX Security 21). 2021: 2633-2650.</p>

1. 本文从语言模型生成大量文本，使用成员推断攻击的方法对生成的序列进行评分，得分最高的序列被归类为属于训练数据，从而从LM中恢复训练数据。
2. 通过攻击可以获取包括个人身份信息（姓名、电话号码和电子邮件地址）、IRC 对话、code和 128 位 UUID等隐私数据。

--
#### 去除重复数据
<p>Deduplicating Training Data Mitigates Privacy Risks in Language Models. arXiv.</p>

1. 本文研究这种攻击的成功与训练数据中序列级重复的关系，分析重复次数与成员推断方法的检测效果关系，并测试在去重数据上重新训练的模型的隐私风险。
2. 得出结论：
	1. 模型再生训练序列与其在训练数据中的重复次数呈超线性关系；
	2. 成员推断方法主要检测出重复多次的序列；
	3. 在去重数据上训练的模型泄露的训练数据更少，成员推断效果较差。

--
#### 结合差分隐私
1. 利用差分隐私方法对数据加噪声$\Rightarrow$导致下游任务性能降低，隐私和效用之间的trade off
2. 如何在提升隐私性的同时尽可能避免效用损失？

--
#### 结合差分隐私
<p>Large Language Models Can Be Strong Differentially Private Learners. arXiv. </p>

1. 微调适当的超参数和任务目标，提高了预训练模型的性能；
2. 内存节省技术，降低DP-SGD的计算开销

--
#### 结合差分隐私
<p>Ullah, Imdad et al. Privacy Preserving Large Language Models: ChatGPT Case Study Based  Vision and Framework. arXiv</p>

1. 结合差分隐私和强化学习进行训练，对数据用差分隐私加噪声，在训练过程中测量隐私损失并评估不确定性或随机性的度量。
2. 在每次更新期间，当出于训练目的添加新信息时，它进一步递归地评估隐私保护的水平以及公共数据库和资源的不确定性度量。

--
#### 结合差分隐私
<p>EW-Tune: A Framework for Privately Fine-Tuning Large Language Models with Differential Privacy. arXiv</p>

1. 基于Edgeworth Accountant设计了通用框架EW-Tune，计算需要添加到随机梯度下降中的噪声量，以保证一定的隐私预算。
2. 与最先进的方法相比，能减少 SGD 的噪声。提高了模型的学习能力和准确性。

--
#### 结合差分隐私
<p>Yansong Li, Zhixing Tan, and Yang Liu. Privacy-Preserving Prompt Tuning for Large Language Model Services. arXiv</p>

1. 直接用已有的差分隐私算法进行加密。
2. 关注加密后导致的模型性能下降问题，提出了token重建方法，在训练数据中的每个输入前添加一系列token，它由任意选择的token组成，可以安全发送而不会引起隐私泄露。
3. 要求LLM从表示中恢复原始的明文token。为了实现这一目标,LLM需要学习更好地表示和理解原始的语义内容,这有利于下游任务。
4. 训练过程中加入token重建任务,相当于增强了LLM对抗加密和随机扰动的能力。这能提高模型对加密后数据的理解力。

--
#### 结合差分隐私
<p>Just Fine-tune Twice: Selective Differential Privacy for Large Language Models</p>

1. 语言数据中的敏感信息往往比较稀疏，这为选择性地只保护敏感信息提供了可能。
2. 首先用去识别敏感信息的数据微调模型。
3. 然后用包含敏感信息的原始数据和隐私优化器继续微调模型。
4. 设计了不同级别的隐私检测器,研究它们对结果模型的影响。

---
### 预测阶段添加扰动
<p>Differentially Private Decoding in Large Language Models</p>

1. 基于输出概率分布扰动的 LLM 中 DP 预测的简单且计算量轻的方法。
2. 语言模型预测下一个词时，会输出一个概率分布向量，表示各个词被预测作为下一个词的概率。
3. 该机制将这个概率向量与均匀分布向量进行线性插值，得到一个扰动后的概率分布，然后根据这个扰动后的分布随机采样，得到预测词。

---
### 知识遗忘
<p>Knowledge unlearning for mitigating privacy risks in language models. ACL2022</p>

1. 选择需要遗忘的目标文本序列(比如包含隐私信息的句子），计算这些序列在当前语言模型下的损失函数，采用最大似然估计；
2. 改变优化目标，进行梯度上升而不是梯度下降；
3. 保持其他超参数不变，在语言模型的参数空间中进行更新，通过一定迭代逼近最大化目标；
4. 重复上述过程,直到目标序列被提取的可能性降到一个可接受的阈值，此时可以认为目标序列已经被“遗忘”。
5. 关键在于如何权衡需要遗忘的信息量,选择最优的遗忘策略,才能在保护隐私的同时最大限度地保持模型效果。这需要进一步的研究。

--
<p>Who's Harry Potter? Approximate unlearning in LMMS. ICLR2024 under review</p>

1. 提出了一种新颖的技术，用于从大型语言模型（LLMs）中去除其训练数据的子集，而无需从头重新训练模型。
2. 该技术由三个主要组成部分构成：通过加强模型识别与去除目标数据相关的token，将目标数据中的特殊表达替换为通用对应项，以及通过这些通用对应项对模型进行微调，从而在提示与上下文相关时有效擦除模型对原始文本的记忆。
4. 两种方法：“通过强化Bootstrapping获得通用预测”和 “通过锚定属于获得通用预测”。最后将这两种方法进行了结合。这些方法旨在让模型生成与"Harry Potter"无关但又合适的文本，从而减少模型对特定文本的记忆。

---
### 风险检测
<p>ProPILE: Probing Privacy Leakage in Large Language Models</p>

1. 关注个人可识别信息（PII）的泄露问题，提出了PII的两个属性：
	1. 关联性表示目标PII与数据主体的其他PII建立关联的可能性，例如一个电话号码和其数据主体的邮箱相关联。
	2. 结构性表示PII的模式是否规整，比如电话号码、邮箱，可通过正则表达式检测。
2. 提出了一个新颖的工具ProPILE，用于探测LLM服务中潜在的PII泄露。ProPILE让数据主体可以根据自己的PII构建提示，来评估LLM泄露PII的可能性。在Pile数据集上训练的OPT-1.3B模型上的应用案例。

---
### 几个方法的总结
#### 训练阶段处理数据：
1. 优点：可以通过适当的参数设置降低隐私保护对模型性能的影响。
2. 缺点：
	1. 预处理的方式，隐私信息可能上下文相关，不易识别，很难找到通用处理方法；
	2. 差分隐私的方式，计算成本高，收敛速度慢，需要对隐私边界进行统一定义；
	3. 处理好数据后可能需要重新训练模型，同时在加密的数据上进行训练效果也会有所下降。

--
#### 预测阶段添加扰动：
1. 优点：只针对预测阶段的扰动无需重新训练模型，计算量很小，可以方便地应用到已训练好的语言模型中，在不损害模型性能太多的情况下提升模型的隐私保护水平。
2. 缺点：由于直接作用于语言模型的输出上，所以对模型性能有一定的负面影响。随着隐私水平的提高，模型效用会下降。

--
#### 知识遗忘：
1. 优点：不需要重新训练大模型，更高效，可以针对性地遗忘某些特定信息，而对语言模型的整体性能影响很小。
2. 缺点：
	1. 一次遗忘较多内容会导致性能下降，可通过顺序遗忘缓解，但遗忘太多依旧有影响；
	2. 不同领域的数据遗忘难度不同，遗忘模型可能不通用。

#### 风险检测：
1. 仅针对特殊数据，对上下文相关的隐私不敏感；
2. 对不同的模型并不通用。

---
### 可考虑的研究方向
1. 隐私策略问题——本质更偏向博弈论的策略选择
	1. 差分隐私的隐私预算策略；
	2. 知识遗忘的策略。
2. 寻找其他替代差分隐私的隐私方案，同态加密、联邦学习、零知识证明等
3. 大部分加密算法其实在NLP有应用，但是迁移到LLM时最大的问题是计算量太大
