---
title: '论文记录-Optimal and Quantized Mechanism Design for Fresh Data Acquisition'
date: 2021-11-01 16:58:36
tags: 
  - crowdsourcing
  - mechanism design
categories: 论文
description: 和虚拟成本有关的一篇论文，师弟的idea中用到了，因此看一看。
---
# 新鲜数据采集的优化量化机制设计

## 摘要

## Introduction

## Related Work

## System Model and Problem Formulation

### System Model

数据源集合：$\mathcal{I}=\{1\leq i\leq I\}$，生成数据包并发送给目标

目标：需要平衡与数据新鲜度相关的成本和与数据本身的货币成本

数据源：需要平衡收益、采样成本和更新频率

1. 数据更新与调度：
   
   随时生成模型——数据源可以在目标要求时生成并发送数据；忽略数据传输延迟。
   
   目标的数据获取策略：更新策略$\mathcal{X}$和调度策略$\mathcal{S}$
   
   ​    更新策略：表示两次更新间隔时间的集合，$\mathcal{X}=\{x_k\}_{k\in \mathbb{N}}$，$x_k$表示第k-1次更新和第k次更新之间的时间间隔
   
   ​    调度策略：用来指定选哪个数据源来更新的二值集合，$\mathcal{S}=\{s_{i,k}\}_{i\in\mathcal{I},k\in\mathbb{N}}$，$s_{i,k}=1$表示第k次更新选了第i个数据源，反之为0表示没选这个数据源，每次更新只选择一个源，因此$\sum_{i\in \mathcal{I}} s_{i,k}=1, s_{i,k}\in\{0,1\}$
   
   ​    第i个数据源的第k-1次更新到第k次更新之间的时间间隔用$y_{i,k}$表示，计算方法是把中间的$x_k$加起来
   
   ​    每个数据源的更新受限于最大更新频率：$f_{i,max}$

2. 信息年龄AoI：时间t的AoI定义：$\Delta_t\{\mathcal{X}\}=t-U_t$，$U_t$表示在t时刻之前的最近一次更新时间

3. 数据源采样成本和隐私信息：
   
   第i个数据源的单位采样成本是$c_i$，是私有信息，认为该成本在$\mathcal{C}_i=[\underline{c}_i,\hat{c}_i]$，
   
   累计分布函数和概率密度函数分别是$\Gamma_i(c_i)$和$\gamma_i(c_i)$，是公开信息

4. 目标的AoI成本：
   
   AoI成本函数$g(\Delta_t(\mathcal{X}))$，用于表示目标对数据陈旧的不满程度，是以AoI为自变量的一个非负增函数
   
   目标的累计AoI成本：$G(x)=\int_0^x g(\Delta_t)d\Delta_t$，表示时间范围x内的累计成本，是凸函数（二阶导大于0的那种）

### Mechanism Design and Reporting Game

数据源和目标之间的两阶段博弈：

1. 目标设计机制$m=(\mathcal{P},\mathcal{X},\mathcal{S})$，并发布给数据源，分别是支付策略、更新策略和调度策略，这三个都是以数据源的报告成本$\widetilde{c}_i$为自变量的函数，目标支付的报酬会根据更新频率和数据源的选择而不同。
2. 数据源提交自己的采样成本$\widetilde{c}_i$，可能是假的

#### 多源系统

报告博弈(Reporting Game) $\mathcal{G}=\{\mathcal{I},\{\mathcal{C}_i\}_{i\in I},\{\mathcal{P}_i\}_{i\in I}\}$

玩家：所有数据源$\mathcal{I}$的集合

策略空间：每个数据源$i$的报告策略是$\widetilde{c}_i\in C_i$

收益：每个数据源$i$的收益函数如下

$$
P_i(\widetilde{c}_i,\widetilde{c}_{-i},m)=lim\ inf_{K->\infty}\frac{\sum_{k=1}^K s_{i,k}(\widetilde{c})(p_{i,k}(\widetilde{c})-c_i)}{\sum_{k=1}^K x_k(\widetilde{c})}
$$

这个收益函数的解释：每个数据源的收益是一个长时间累积收益的单位时间平均值（这里是净收益，也就是到手的报酬减去成本）

根据一些参考文献，一个深思熟虑的数据源并不是策略性的，而是短视的，即：并不会最大化他们可见的 长期收益（也就是上面这个公式）

由于每个数据源都不知道其他数据源的具体成本$\widetilde{c}_{-i}$，只知道相关的先验分布，因此可得到下述贝叶斯均衡：

**定义1**（贝叶斯均衡）：贝叶斯均衡是：对于每一个数据源$i$，在机制$m$和其他数据源的报告$\widetilde{c}_{-i}$不改变的情况下，使得该数据源收益最大的那个报告$$\tilde{c}^{*}(m)=\left\{\tilde{c}_{i}^{*}(m)\right\}_{j \in \mathcal{T}}$$

而目标的目的是设计一个最优机制来最小化自己的总期望支出（长期平均值），总支出由AoI支出和数据源支出组成，函数如下：

$$
J(m)=\mathbb{E}_{\boldsymbol{c}} \left[\operatorname { lim sup} _ { K \rightarrow \infty } \left(\frac{\sum_{k=1}^{K} G\left(x_{k}\left(\tilde{\boldsymbol{c}}^{*}(m)\right)\right)}{\sum_{k=1}^{K} x_{k}\left(\tilde{\boldsymbol{c}}^{*}(m)\right)}\right.\right.
+\left.\left.\frac{\sum_{k=1}^{K} \sum_{i \in \mathcal{I}} s_{i, k}\left(\tilde{\boldsymbol{c}}^{*}(m)\right) p_{i, k}\left(\tilde{\boldsymbol{c}}^{*}(m)\right)}{\sum_{k=1}^{K} x_{k}\left(\tilde{\boldsymbol{c}}^{*}(m)\right)}\right)\right],
$$

其中$\tilde{\boldsymbol{c}}^{*}(m)$是贝叶斯均衡。

每个数据源都有动机谎报成本，根据显示原理，对于任意机制$m$，一定存在一个激励相容机制$\tilde{m}$，使得$J(m)=J(\tilde{m})$

总结一下这里就是说，我们设计出的机制要满足激励相容约束和个体理性约束，前者保证数据源如实上报，后者保证数据源期望收益非负，这两个约束写成公式的形式如下：

$$
\mathrm{IC}: \quad c_{i} \in \arg \max _{\tilde{c}_{i} \in \mathcal{C}_{i}} \mathbb{E}_{\boldsymbol{c}_{-i}}\left[P_{i}\left(\tilde{c}_{i}, \boldsymbol{c}_{-i}, m\right)\right], \quad \forall i \in \mathcal{I}
$$

$$
\operatorname{IR}: \quad \max _{\tilde{c}_{i} \in \mathcal{C}_{i}} \mathbb{E}_{c_{-i}}\left[P_{i}\left(\tilde{c}_{i}, \tilde{\boldsymbol{c}}_{-i}^{*}(m), m\right)\right] \geq 0, \quad \forall i \in \mathcal{I}
$$

#### 单源系统

只有一个数据源的特殊情况下，不存在数据源之间的博弈，激励相容和个体理性简化成下述形式：

$$
\begin{array}{ll}
\mathrm{IC}-\mathrm{S}: & c \in \arg \max _{\tilde{c} \in \mathcal{C}} P(\tilde{c}, m) \\
\mathrm{IR}-\mathrm{S}: & \max _{\tilde{c} \in \mathcal{C}} P(\tilde{c}, m) \geq 0
\end{array}
$$

### Problem Formulation

目标需要找一个在满足约束的前提下最小化自己支出$J(m)$的机制$m$，约束包括：

1. 一轮选一个数据源；
2. 更新频率不能超过数据源的上限；
3. 激励相容约束
4. 个体理性约束

接下来证明了一类最优机制。

**定义2**（等间距和统一费率机制）：一个机制$m=(\mathcal{P},\mathcal{X},\mathcal{S})$在满足以下条件时，我们称之为等间距和统一费率机制：

$$
p_{i, k}(\cdot)=p_{i}(\cdot) \text { and } x_{k}(\cdot)=x(\cdot), \quad \forall k \in \mathbb{N}, i \in \mathcal{I}
$$

其中，$p_{i}: \mathcal{C} \rightarrow \mathbb{R}_{+}$ ，$x: \mathcal{C} \rightarrow \mathbb{R}_{+}$。

意思就是对于数据源$i$，每一轮支付给它的钱都是一样的；而每一轮的更新频率也设置成一样的。

**定义3**（*随机*固定调度）：一个调度策略$\mathcal{S}$在满足下述描述时，我们称之为固定调度：如果对于所有的数据源$i$，给定任意成本$c$，每个时间$k$的调度策略$s_{i,k}(c)$都是随机选择且在$k$上独立同分布，并且满足：

$$
\operatorname{Pr}\left(s_{i, k}(\tilde{\boldsymbol{c}})=1\right)=\pi_{i}(\tilde{\boldsymbol{c}}), \quad \forall \tilde{\boldsymbol{c}} \in \mathcal{C}, k \in \mathbb{N}, i \in \mathcal{I}
$$

其中，$\pi_{i}: \mathcal{C} \rightarrow[0,1]$ 满足 $\sum_{i \in \mathcal{T}} \pi_{i}(\tilde{\boldsymbol{c}})=1$

意思就是固定调度每一轮的调度策略与这一轮的时间$k$无关。

接下来说明最优机制的一些特性。

**引理1**：

## Single-Source Optimal Mechanism Design

## Multi-source Optimal Mechanism

## Quantized Optimal Mechanism

## General Virtual Cost Function

## Performance Comparison

## Conclusion