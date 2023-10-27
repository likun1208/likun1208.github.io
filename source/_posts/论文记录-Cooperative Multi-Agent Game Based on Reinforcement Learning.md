---
title: 论文记录-Cooperative Multi-Agent Game Based on Reinforcement Learning
date: 2023-10-18 10:51:36
tags:
  - game theory
  - reinforcement learning
  - 审稿意见
categories: 论文
description: HCC的审稿
---
 # Intro
1. 多主体控制研究普遍用计算机游戏来测试
2. 现有多智能体强化学习算法面临问题;
	1. 需要大量资源和时间
	2. 没有重视智能体之间的通信
	3. 难以应对奖励少的挑战
3. 本文：关注智能体协作，分为三阶段策略：
	1. 具有反事实基线的中央评估网络，评估每个智能体的独立动作，同时大大降低了计算复杂度
	2. 图双层注意力网络：管理智能体之间的通信信号，通过促进有效的沟通，该网络提高了决策的准确性和效率
	3. 基于势函数的奖励塑造方法，旨在提高算法的收敛速度和稳定性，同时适应奖励稀疏的环境
# 框架
1. 评委网络：
	1. 输入其他智能体的动作、环境的状态、智能体的观察、正在计算的智能体的数量以及当前智能体在前一次的动作
	2. 输出当前智能体的每个动作的一组动作值函数
2. 演员网络：输入来自环境的观察向量并输出每个动作的概率分布。计算出向量后，可以使用贪婪策略或探索策略来选择动作
3. 通信单元：使用循环神经网络对每个智能体的观察进行编码，并将其作为通信信号在智能体之间传输信息
## 具有反事实基线的评委网络
这部分直接用了文献23的算法，目前来看没创新，简单记录一下：
1. 传统MAAC算法在智能体变多时，网络规模指数增长；
2. 文献23的算法解决了这个问题。

不过根据我查到的资料，似乎MAAC是在文献23的基础上做的，感觉不太对劲。
## 具有图注意力网络的演员网络
基于图形注意力网络设计了一种带有通信模块的Actor网络结构，在保持Agent独立性的同时增加了处理通信信号的能力。
# 具有反事实基线的多主体演员-评委
## 算法框架
1. MADDPG引入策略梯度算法，但是有局限性，不能解决去中心化问题，且网络很大，训练成本高
2. 为了解决这个问题，文献27设计了参数共享
3. 基于一些原因，本文目标：设计一种AC算法
	1. Actor网络以agent的观察历史序列和动作历史序列作为输入，输出动作的概率分布； 
	2. Critic网络以所有智能体的全局状态、观察历史序列、联合动作历史序列和Actor网络的策略作为输入，输出智能体所采取的动作的状态值函数。
4. Critic网络可以使用𝑇𝐷(𝜆)[28]进行训练。它根据当前状态的奖励和接下来𝑛步骤获得的奖励计算加权累积收益，并可以根据从当前状态到终止状态的所有状态更新当前状态的状态值函数。
5. Actor网络直接使用策略梯度方法来更新网络，但是这样集中式的训练使得大家共享同一个网络，没考虑信用值，有误差
6. 文献29引入了差异奖励的思想，用了不同的奖励函数。
7. 由此需要计算默认行为带来的奖励，然后和实际行为作比较。但是一般来说是没法获取默认行为的结果，因此文献23设计了反事实奖励函数
8. 结合上述内容，本文设计了CMAC算法，算法主体采用MAAC结构。
	1. Critic网络获取当前时刻其他智能体的动作、状态、观察值、正在计算的智能体的数量以及当前智能体在上一时刻的动作，并为每个动作输出一组动作值现任代理人。 
	2. Actor网络获取环境的观察向量并输出每个动作的概率分布。可以使用贪婪策略或探索策略来选择操作。
# 基于图注意力网络的通信模块
1. 总体而言：
	1. 首先，我们使用硬注意力网络来过滤需要通信的代理
	2. 然后使用自注意力[30]网络对通信信号进行加权
	3. 通过两阶段注意力，我们通过降低计算成本和提高信息的信噪比来整合复杂的通信信号，从而在不影响Actor网络的情况下实现Agent之间的通信
2. 多智能体模型下，每个智能体存在局限性，会影响整个系统。因此通信是必要的。
3. 本文提出图注意力通信单元：
	1. 使用 BiGRU 的编码层，对观测信息编码
	2. 硬注意力层。该层通过编码层的编码输出$h_{i,j}$来决定$agent_i$和$agent_j$之间通信的必要性，用了文献31的方法
	3. 自注意力层。该层的输入是代理观察$e=[e_1,e_2, ⋯ ,e_n]$的编码，输入向量的注意力权重由自注意力网络计算。通过这个网络，来自不同代理的通信信号被加权。自注意力机制对过滤后的通信信号的重要性进行判断，使得重要信号在隐藏状态具有较高的权重。
	4. 输出层。它将隐藏状态$e$与前一层的$W_h$和$W_{sj}$ 输出聚合，并生成扩展的隐藏状态向量。然后它使用MLP对向量进行解码，并输出策略𝜋，它是代理每个可能动作的动作概率.
# 基于势函数的奖励塑造
本节设计了基于静态和动态势能的奖励塑造函数，以解决策略学习速度慢、效果差的问题。在引入先验知识和对环境的认知后，设计了一种人为增加势能差的奖励函数来辅助学习算法，将先验知识转化为额外的奖励，从而指导所提出的学习算法学习得更快更好。
## 静态势函数
结构很简单，我方当前血量+我方存活单元-敌方当前血量-敌方存活单元。与仅从一种奖励函数中学习相比，智能体通过拉开智能体健康状况的距离来积累优势来学习如何玩游戏，而不是采取随机行动。
## 动态势函数
动态势能函数则考虑到同一状态的势能可能随时间的推移而变化，同一状态转换可能具有不同的奖励，在静态函数的基础上增加了能量损失速度的控制变量和随时间变的权重函数（描述时间流所带来的敌人状态重要性的变化）。势函数的设计围绕一系列游戏规则展开。
# 实验
1. 在基于星际争霸2的游戏平台进行实验，和游戏内置AI对打
2. 对比结果说明这个方法胜率高

# 总体意见
1. 内容概述：
With the advancement of technology, multi-agent reinforcement learning (MARL) has been extensively applied across various domains. To effectively address challenges in MARL, this paper investigates a multi-agent Actor-Critic framework based on graph attention networks. By refining the reward allocation mechanism, communication and collaboration modality, as well as reward function design, this algorithm enhances performance. Experiments in the StarCraft multi-agent environment demonstrate over 95% winning rate.

2. strength：
	1. The paper proposes a novel algorithm GACMAC using graph attention networks to enhance collaboration among multiple agents in reinforcement learning.
	2. The innovations of the paper lie in utilizing counterfactual baseline and graph attention networks to strengthen cooperation among agents, and incorporating potential-based reward shaping to accelerate algorithm convergence.
	3. Experiments on the StarCraft multi-agent platform demonstrate the validity of the methods. The algorithm shows certain novelty and practical values.
	4. The figures in the paper vividly illustrate the framework and architecture of the proposed algorithm, which helps explain the algorithm flow and components effectively.

3. weakness：
	1. The paper does not sufficiently explain the main differences between the proposed GACMAC algorithm and existing algorithms such as MAAC and COMA in terms of theory, network structure, etc. It is recommended that the author adds more comparisons and discussions in this aspect.（去掉）
	2. The references are not new enough, with most dated before 2021, failing to reflect the latest progress in the field over the past two years. It is recommended that the authors add some references from the last two years.
	3. It is suggested that the notational representation used in this paper be introduced first in the System framework section, thus making it easier for the reader to understand the algorithmic framework illustrated in Figure 1.