---
title: 论文记录-LLM+Privacy Survey总结
date: 2023-12-22 14:25:00
tags:
  - LLM
  - Privacy
  - Security
categories: 论文
description: LLM+Privacy的几篇survey
---
# LLM+Privacy Survey总结
## A Survey on Large Language Model (LLM) Security and Privacy: The Good, the Bad, and the Ugly
1. 这篇survey探讨的是LLM对安全与隐私的影响，分别从以下三方面讨论：
	1. Good：利用LLM来解决一些安全隐私问题，例如用LLM查代码漏洞；
	2. Bad：利用LLM来完成攻击，例如社工；
	3. Ugly：探讨LLM的漏洞和防御，这部分和我们要关注的隐私推断相关，因此接下来只看这部分。
2. 对几个LLM模型的对比方面，这篇论文列了`Date Provider`、`Open-Source`、`Params`和`Tunability`这四方面，对比了`gpt-4,gpt-3.5-turbo,gpt-3,cohere-medium,cohere-large,cohere-xlarge,BERT,T5,PaLM,LLaMA,CTRL,Dolly 2.0`这几个模型，我觉得我们可以加上谷歌的`claude,bard,gemini`和一些国产模型。
3. 论文的图表都挺好，有参考价值
### LLM的漏洞和威胁
#### AI模型固有漏洞
1. 对抗性攻击：用于故意操纵或欺骗机器学习模型的技术和策略
	1. 文本下毒：向训练数据集中注入恶意数据来影响训练过程
	2. 后门攻击：恶意操纵训练数据和模型处理，从而创建一个漏洞，攻击者可以将隐藏的后门嵌入到模型中
2. 推理攻击：攻击者试图通过对模型进行特定查询或观察来获取有关机器学习模型或其训练数据的敏感信息或见解
	1. 属性推理：攻击者试图通过分析机器学习模型的行为或响应来推断个人或实体的敏感或个人信息
	2. 成员推理：在给定对模型和特定数据记录的白/黑盒访问的情况下，确定数据记录是否是模型训练数据集的一部分
3. 提取攻击：攻击者试图从机器学习模型或其相关数据中提取敏感信息或见解。提取攻击和推理攻击有相似之处，但具体重点和目标不同。提取攻击旨在直接获取特定资源（例如模型梯度、训练数据）或机密信息。推理攻击通常通过观察模型的响应或行为来获取有关模型或数据特征的知识或见解。
4. 偏见与不公平：
5. 指令调整攻击：
	1. 越狱：
	2. 提示词注入：
#### 非AI模型固有漏洞
