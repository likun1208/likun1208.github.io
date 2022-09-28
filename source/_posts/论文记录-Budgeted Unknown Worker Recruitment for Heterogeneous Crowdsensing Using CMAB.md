---
title: 论文记录-Budgeted Unknown Worker Recruitment for Heterogeneous Crowdsensing Using CMAB
date: 2022-09-11 16:02:25
tags: 
    - crowdsourcing
    - Combinatorial Multi Armed Bandit
category: 论文
---

<!--more-->

这篇论文是在[之前看过的论文](https://likun1208.github.io/2020/07/05/%E8%AE%BA%E6%96%87%E8%AE%B0%E5%BD%95-Combinatorial%20Multi-Armed%20Bandit%20Based%20Unknown%20Worker%20Recruitment%20in%20Heterogeneous%20Crowdsensing/https://likun1208.github.io/2020/07/05/%E8%AE%BA%E6%96%87%E8%AE%B0%E5%BD%95-Combinatorial%20Multi-Armed%20Bandit%20Based%20Unknown%20Worker%20Recruitment%20in%20Heterogeneous%20Crowdsensing/)的基础上扩展的，因此前面的内容暂时先不看，直接看多出来的部分，这部分针对的问题是一些workers总被分配任务而另一些则没有任务。

## FAIRNESS CONSTRAINT OF WORKERS

公平性：众包平台必须保障每一个worker的最小选择频率，从而避免出现一些workers任务很多而另一些没有任务的场面。

因为：没任务的workers会永久性离开平台，导致workers数量减少，这有损于其他requesters的数据结果。因此公平性约束潜在地保障了所有requesters的全局长期传感性能。

方法：

1. 引入参数$\eta_i$：多轮招募中第$i$个worker至少被招募的部分

2. 公平性约束：$\liminf _{B \rightarrow \infty} \mathbb{E}\left[n_i(\tau(B))\right] \geq \tau(B) \cdot \eta_i$, for $\forall i \in \mathcal{N}$
   
   其中，$\tau(B)$表示预算为B时招募的总轮数，$n_i(\tau(B))$表示预算为B时第i个worker被招募的总轮数

3. 引入虚队列，队列长度计算：$V_i(t)=\max \left\{0, V_i(t-1)+\eta_i-\mathbb{I}\left\{i_{t-1}=i\right\}\right\}$

4. 将队列长度也纳入多臂赌博机的计算公式中：
   
   $$
   p_{i}^{l}=\underset{p_{i^{\prime}}^{l^{\prime}} \in\left(\mathcal{P} \backslash \mathcal{P}^{t}\right)}{\operatorname{argmax}}\left\{\frac{u_{\left[\widehat{q}_{i}(t-1)\right]}\left(\mathcal{P}^{t} \cup\left\{p_{i^{\prime}}^{l^{\prime}}\right\}\right)-u_{\left[\widehat{q}_{i}(t-1)\right]}\left(\mathcal{P}^{t}\right)}{c_{i^{\prime}}^{l^{\prime}}}+\varrho \cdot V_{i}(t)\right\}
   $$
   
   

5. 其余内容没有改变
