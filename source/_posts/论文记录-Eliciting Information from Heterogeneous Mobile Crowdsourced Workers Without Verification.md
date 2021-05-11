---
title: 论文记录-Eliciting Information from Heterogeneous Mobile Crowdsourced Workers Without Verification
date: 2021-04-15 14:00:13
tags:
	- crowdsourcing
	- game theory
	- Stackelberg game
categories: 论文
description: 众包中的多数投票--探索众包工人异构性对激励机制设计的影响
---

# Eliciting Information from Heterogeneous Mobile Crowdsourced Workers Without Verification

这篇和Strategic Information Revelation in Crowdsourcing Systems Without Verification是同一批作者的，针对的问题一样，内容方法看着也差不多，希望别一模一样......

<!--more-->

## Abstract

问题：无验证的信息提取（IEWV）

思路：激励workers高质量和如实报告

常规方法：多数投票——符合多数解决方案的workers获得报酬

存在问题：未考虑workers的异构性

本文方法：两阶段Stackelberg博弈

## Introduction

本文场景：

1. workers求解准确率异构（即，不同workers以不同概率生成高质量数据）
2. 考虑数据质量与平台成本之间的平衡

本文方法：多数投票机制

需要解决的问题：

1. 给定奖励，多数投票机制下，异构workers会如何行动；
2. workers的异构性如何影响平台的最优奖励设计和均衡；
3. 在提升平台均衡方面，知道workers异构方案准确性有什么价值。

本文的两阶段博弈：

1. 平台决定多数投票机制的奖励等级，目标是最大化收益，该收益考虑了workers解决方案质量和平台总支出之间的均衡；
2. 每个worker选择自己的努力等级和报告策略，目标是最大化自己收益。

本文考虑两种情况：

1. 不完全信息博弈（平台不知道workers的准确率）
2. 完全信息博弈（平台知道workers的准确率）

### Key Contributions

1. 分析了IEWV问题中异构workers场景下的平台均衡
2. 分析了多均衡共存的场景
3. 刻画了workers的最优策略
4. 刻画了workers异构性对平台均衡的影响
5. 刻画了得知workers异构性的价值

## Related Work

### Information Elicitation

略

### Information Aggregation

略

## Model

### Workers' Decision Problem

#### Workers and Task

集合：$N=\{1,2,...,N\}$表示到达平台的workers

待求解问题的答案是二选一，记作$X=\{-1,1\}$

$x\in X$表示该任务的正确答案

$x_i^{estimate}\in X$表示第$i$个worker对任务答案的估计值

$x_i^{estimate}\in X$表示第$i$个worker向平台上报的任务答案

#### Worker Effort Exertion and Reporting Strategy

workers的任务质量依赖于努力程度，记作$e_i\in \{0,1\}$

workers可通过付出一定代价来努力从而提高任务质量（即准确率）

第$i$个worker的求解方案准确率记作$p_i\in (0.5,1])$
$$
P(x_i^{estimate}=x)=\left\{
\begin{matrix}
p_i,\ \ &if\ \ e_i=1\ \ with\ \ a\ \ cost\ \ c_i\geq 0, \\
0.5,\ \ &if\ \ e_i=0.
\end{matrix}
\right.
$$
workers异构性的体现：$N$个workers中有$k$个高质量的求解准确率为$p_h$，和$N-k$个低质量的求解准确率为$p_l$，$0.5<p_l<p_h\leq 1$

workers的上报策略：$v_i=\{1,-1,rd\}$，其中，努力的workers可以选择$\{1,-1\}$分别表示如实报告和谎报；而不努力的workers可以选择$rd$表示以0.5的概率随机上报
$$
x_i^{report}=
\left\{
\begin{matrix}
x_i^{estimate},\ \ &if\ \  v_i=1\\
-x_i^{estimate},\ \ &if\ \  v_i=-1\\
1\ \  or\ \  -1\ \  with\ \  an\ \  equal\ \  probability,\ \ &if\ \  v_i=rd.
\end{matrix}
\right.
$$
本文不考虑多个workers共谋等情况。

workers的策略记作$s_i=(e_i,v_i)$，属于集合$S_i=\{(0,rd),(1,1),(1,-1)\}$

#### Internal Reward for Truthful Reports

第$i$个worker如果努力且如实上报，则会得到内在奖励$l_i\geq 0$

反之，谎报会得到内在惩罚$-l_i$

不努力的worker会随机上报，他的内在奖励为0

#### Consistency Reward for Output Agreement

一致性奖励记作$R\geq 0$

除了第$i$个worker以外，其余workers的多数投票方案记作$x_{-i}^{majority}$

从第$i$个worker的角度来看，多数投票方案是：
$$
x_{-i}^{majority}=
\left \{
\begin{matrix}
1,\ \ if\ \ \sum_{j\in N,j\neq i}x_j^{report}>0,\\
-1,\ \ if\ \ \sum_{j\in N,j\neq i}x_j^{report}<0,\\
tie,\ \ if\ \ \sum_{j\in N,j\neq i}x_j^{report}=0.
\end{matrix}
\right.
$$
第$i$个worker的上报值与多数投票方案一致或者多数投票方案是tie的情况下，该worker会得到奖励；反之则不会。

第$i$个worker得到奖励R的概率记作$P_i(R,p,s)$，$p=(p_j,\forall j\in N)$，$s=(e,v)$，$e=(e_j,\forall j \in N)$，$v=(v_j,\forall j \in N)$

#### Worker Payoff Maximization Problem

worker的收益函数：$u_i(R,s)=R*P_i(R,p,s)+v_il_i-e_ic_i$

**博弈1：**$\Omega_1=(N,S,u)$，其中：

1. $N$：N个workers的集合

2. $S$：第$i$个worker的策略空间为$s_i\in S_i=\{(0,rd),(1,1),(1,-1)\}$。所有workers的策略记作$s=(s_i,\forall i\in N)$，所有workers的可行策略集合记作$S=\prod_{i\in N}S_i$。

   *这里应该是集合的笛卡尔积，不过这个表达不太确定是否正确。*

3. $u$：向量$u=(u_i,\forall i \in N)$包含了所有workers的收益

该博弈中，给定报酬$R$和其他workers的决策$s_{-i}=(e_{-i},v_{-i})=((e_j,v_j),\forall j \neq i)$，第$i$个worker需要求解下述优化问题。

**问题1：**第$i$个worker的收益最大化

$max\ \ u_i(R,s,s_{-i})$

$var.\ \ s_i\in\{(0,rd),(1,1),(1,-1)\}.$

**定义1：**纳什均衡：在给定$R$的情况下，如果对于所有$s_i'\in S_i$，和所有$i\in N$，都有$u_i(R,s_i^*,s_{-i}^*)\geq u_i(R,s_i',s_{-i}^*)$，则认为策略$s^*=\prod_{i\in N}s_i^*$构成了博弈$\Omega_1$的一个纳什均衡。

注意：第$i$个worker的策略选择是前面**问题1**的优化解决方案，是与平台支付报酬$R$和其他worker策略$s_{-i}$相关的函数。

### Platform's Reward Design Problem

#### Accuracy of Aggregated Estimate

平台根据所有workers的上报值进行多数投票猜测最终的值
$$
x_p^{estimate}=
\left \{
\begin{matrix}
1,\ &if\ \sum_{i=1}^Nx_i^{report}>0,\\
-1,\ &if\ \sum_{i=1}^Nx_i^{report}<0,\\
1\ or\ -1\ with\ an\ equal\ probability,\ &if\ \sum_{i=1}^Nx_i^{report}=0.
\end{matrix}
\right.
$$
$P_a(R,p,N)$表示上述公式得到的聚合解决方案是正确的概率

假设：N是基数且$N\geq 4$

#### Expected Consistency Rewards.

workers总一致性奖励期望记作$E[R^T]$

#### Platform's Payoff Maximization Problem

平台收益函数：
$$
\prod(R)=\beta P_a(R,p,N)-E[R^T]
$$
$\beta>0$表示平台对准确率分配的权重

**问题2：**平台的收益最大化：

$max \prod(R)$

$var. R\geq0$

### Two-Stage Game

阶段1：平台决定R

阶段2：workers选择努力和上报策略

博弈求解方法：逆向归纳

符号表达：

$X$：任务类型空间

$x$：任务真实类型

$x_i^{estimate}$：第$i$个worker的估计值

$x_i^{report}$：第$i$个worker的上报值

$x_{-i}^{majority}$：除了第$i$个worker以外，剩下workers的多数投票方案

$x_p^{estimate}$：平台的聚合估计值

$s_i\triangleq(e_i,v_i)$：第$i$个worker的策略（努力策略和上报策略）

$c_i$：第$i$个worker的努力成本

$l_i$：第$i$个worker的内部奖励

$p_i$：第$i$个worker努力时的准确率

$R$：平台的一致性奖励

$\beta$：平台对聚合准确率的权重

$E[R^T]$：所有workers一致性奖励的期望

## Incomplete Information

每个worker的准确率只有自己知道，其他workers和平台都不知道。

### Workers' Decisions at Stage II

#### Workers' Heterogeneity

workers异构性的体现：

1. 准确率异构——>$k$个高准确率$p_h$workers，用集合$N_h$表示，$N-k$个低准确率$p_l$，用集合$N_l$表示，$1\leq k\leq N-1,0.5<p_l<p_h\leq 1$，这些参数的数值是公开信息。
2. 努力时的成本和内在奖励/惩罚相同——>$c_i=c,l_i=l,l<c$

#### Workers' Equilibrium Behaviors

假设：同类型worker（准确率相同）会选择相同的策略

**定义2：**对称纳什均衡（SNE）

1. n-SNE：所有workers都不努力和如实报告
2. f-SNE：所有workers都努力和如实报告
3. p-SNE：高质量workers努力和如实报告，低质量workers则相反

**命题1：**workers之间的SNE

1. 当$R\geq 0$时一定存在一个n-SNE
2. 存在一个阈值$R_f\geq 0$，当$R\geq R_f$时，一定存在一个f-SNE
3. 当$k\geq k_1$时，存在两个阈值$0<R_p^l\leq R_p^h$，使得在$R_p^l\leq R \leq R_p^h$时，一定存在一个p-SNE

阈值之间的关系取决于$k$的大小：当$k$较小时，$R_f\leq R_p^l$；当$k$较大时，$R_f>R_p^l$。

由命题1可得：

1. 对于任意非负一致性奖励$R$，一定存在一个n-SNE，所有workers都没有动机努力和如实报告；努力worker的收益是$\frac{R}{2}+l-c$，不努力worker的收益是$\frac{R}{2}$，由于$l<c$，显然不努力更好。
2. 当一致性奖励足够大时，存在f-SNE，所有workers都会努力和如实报告。
3. workers准确率的异构性允许我们通过机制来使不同准确率的workers采取不同策略，当一致性准确率在合适范围内时，存在p-SNE使得高准确率workers努力且如实报告，而低准确率workers不努力并随机报告。

各均衡解存在性与参数的关系：

1. $k<k_1:$ 高准确率workers很少时，不存在p-SNE。
2. $k_1\leq k\leq k_2:$ 高准确率workers人数适量时，存在p-SNE，但平台报酬$R$的下限很高（$R_p^l>R_f$）。
3. $k\geq k_2:$ 高准确率workers足够多时，存在p-SNE，且$R_p^l<R_f$。当k很大时，高质量workers在p-SNE得到报酬的概率与在f-SNE得到报酬的概率相近，低质量workers也会被激励选择(1,1)策略。

帕累托最优均衡解：

**命题2：**给定一致性奖励$R$，一定存在一个帕累托最优均衡解。

这意味着可以通过调整一致性奖励来引导worker的努力策略和上报策略。

**命题3：**策略空间（$s_i^*=(0,rd),\forall i\in N_h,s_j=(1,1),\forall i\in N_l$）不存在均衡解。

这意味着不会出现低质量workers努力而高质量workers随机的场面。对于给定的R，高质量workers有更高的概率拿到奖励。在努力成本不变的前提下，高准确率workers更愿意选择(1,1)的策略。换言之，多数投票方案更能吸引高准确率workers。然而，在完全信息的场景下这一条并不成立。

### Platform's Reward Design at Stage I

$P_f,P_p,P_n:$ workers在三种均衡下的聚合准确率

$E[R_f^T],E[R_p^T],E[R_n^T]:$ 三种均衡下的总期望一致性奖励

$\eta_f,\eta_p,\eta_n:$ 三种均衡下的效率（是指一致性奖励的效率）

效率计算方式：

$\eta_f=\frac{P_f-P_n}{E[R_f^T]}$

$\eta_p=\frac{P_p-P_n}{E[R_p^T]}$

$\eta_n=\frac{P_n-P_n}{E[R_n^T]}=0$

上面这个计算式反过来（也就是$\frac{1}{\eta_f}$）的含义是：对于单位准确率而言，从均衡n-SNE提高到f-SNE所需要支出的平均一致性奖励。$\eta_p$也是类似的含义。

平台的最优决策R：

**定理1：**给定任意参数$(\beta,N,c,l,p_l,p_h,k)$，平台可以引导workers达到f-SNE和对应的效率$\eta_f$

1. 如果p-SNE存在，且$\eta_p\geq \eta_f$，平台的最优一致性奖励为：
   $$
   R^* = 
   \left\{
   \begin{matrix}
   0,\ &if\ \beta<\frac{1}{\eta_p}, \\
   R_p^l,\  &if\ \frac{1}{\eta_p}\leq\beta<\frac{E[R_f^T]-E[R_p^T]}{P_f-P_p}\\
   R_f,\  &if\ \beta \geq \frac{E[R_f^T]-E[R_p^T]}{P_f-P_p};\\
   \end{matrix}
   \right.
   $$
   
2. 其他情况下的平台最优一致性奖励为：
   $$
   R^*=
   \left \{
   \begin{matrix}
   0,&\ if\ \beta<1/\eta_f,\\
   R_f,&\ if\ \beta\geq1/\eta_f.
   \end{matrix}
   \right.
   $$

![image-20210507094050695](https://i.loli.net/2021/05/07/E2af8LhbsgiKpUC.png)

![image-20210507094124550](https://i.loli.net/2021/05/07/2PdRjKWyF7prVfT.png)

分析**定理1**可得：

1. 当p-SNE存在，且效率高于f-SNE，对应图4中的$k>k^{THR}$区域，平台会根据$\beta$来引导均衡从而最大化收益。当$\beta$较小时，由于较低的准确率评价，平台不会提供任何激励，R=0，从而n-SNE是帕累托最优，对应图3中$k>k^{THR}$区域中下面的白色部分。当$\beta$合适时，即图3中的蓝色区域，平台会选择$R=R_p^l$从而得到p-SNE。当$\beta$很大时，即图3中的灰色区域，平台会选择$R=R_f$，得到f-SNE，同时在一致性奖励方面支出很大。
2. 当p-SNE不存在，或其效率低于f-SNE，对应图4中的$k<k^{THR}$区域，类似前面的分析，$\beta$较小时，是n-SNE；反之则是p-SNE。

## Complete Information: Weighted Aggregation

每个worker的准确率$p_i$是公开信息

### Workers' Decisions

这一步和前面的**博弈1**、**命题1**一样。

### Platform's Reward Design

平台使用加权多数规则（weighted majority rule）来决定最终的聚合准确率：
$$
x_p^{estimate}=
\left\{
\begin{matrix}
1,\ &if\ \sum_{i=1}^Nw_i(p_i,s_i)x_i^{report}>0\\
-1,\ &if\ \sum_{i=1}^Nw_i(p_i,s_i)x_i^{report}<0\\
1\ or\ -1\ with\ equal\ prob.,\ &if\ \sum_{i=1}^Nw_i(p_i,s_i)x_i^{report}=0
\end{matrix}
\right.
$$
其中，
$$
w_i(p_i,s_i)=log\frac{\rho(p_i,s_i)}{1-\rho(p_i,s_i)}, \\
\rho(p_i,s_i)=
\left\{
\begin{matrix}
0.5,\ &if\ s_i=(0,rd), \\
p_i,\ &if\ s_i=(1,1), \\
1-p_i,\ &if\ s_i=(1,-1).
\end{matrix}
\right.
$$

权重公式既能提高聚合准确率，又会显著影响平台决定的报酬。

第i个worker的权重$w_i(p_i,s_i)$由其准确率和策略共同决定，因此：

1. 在f-SNE时（所有workers都选择`(1,1)`策略），高质量workers有更高权重，


## Complete Information: Discriminatory Reward Policy

### Workers' Decisions

### Platform's Optimal Reward Design

## Numerical Results

### Incomplete Information

### Complete Information

## Conclusion

