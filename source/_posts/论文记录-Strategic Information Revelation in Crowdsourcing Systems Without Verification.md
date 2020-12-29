---
title: 论文记录-Strategic Information Revelation in Crowdsourcing Systems Without Verification
date: 2020-12-25 09:38:43
tags: 
	- crowdsourcing
	- game theory
category: 论文
description: 无验证的众包系统中的策略性信息披露
---

# Strategic Information Revelation in Crowdsourcing Systems Without Verification

## Abstract

1. 本文研究：无需验证解决方案、激励员工提供高质量解决方案的众包平台
2. 本文假设：信息不对称、平台具有信息优势——平台知道有关workers解决方案的平均准确性的更多信息，可以向workers策略性披露信息。根据平台公开的信息，workers判断自己认真完成任务后所获得的奖励。
3. workers类型：
   1. naive workers：完全信任平台公开的信息
   2. strategic workers：基于平台公开信息更新自己的先验信念
4. 本文发现：
   1. 对于naive workers：始终宣布高平均精度
   2. 对于strategic workers：有动机宣布低于实际值的平均精度
   3. 平台的回报可能减少高精度workers 

## Introduction

### Motivation

1. 互联网的发展使得多种在线任务的众包具有可行性。
2. 高质量的众包任务解决方案需要worker付出足够的努力，因此平台需要提供激励；而平台无法获取真实结果验证解决方案时，设计激励方案会很难，进而引出IEWV问题。
3. IEWV(Information elicitation without verification)：未验证的信息挖掘，大多数研究对称信息场景；而实际上，平台往往有更多信息。
4. 本文采用多数投票方案：如果一个worker与其他workers的大多数解决方案相匹配，他将获得一个一致性奖励。
5. 本文假设平台有一个额外决策：信息披露。该场景中，workers由高精度和低精度的混合构成，平台知道每种类型的数量，而workers不知道。
6. 本文研究：
   1. 平台是否有披露信息的动机
   2. 平台是否操纵被披露的信息
   3. 平台的最优信息披露策略——>平台如何利用信息不对称
7. 平台与workers之间的交互（三阶段）：
   1. 平台决定信息披露策略
   2. 平台决定一致性奖励
   3. workers决定是否努力完成任务以及是否如实报告解决方案
   4. 上述三阶段结束后，平台收集workers上报的解决方案并根据三阶段来决定一致性奖励
8. 本文考虑两种类型的workers：
   1. naive workers：完全相信平台、workers推理平台公开信息是否可靠的能力有限，可以作为基准
   2. strategic workers：不相信平台，有很高推理能力

### Key Contributions

1. 研究IEWV问题的策略性信息披露：非凸问题，但是可以利用特殊结构求最优解
2. workers的均衡策略：证明workers之间存在多重均衡，在适当的信息披露和报酬设计下，所有workers努力工作并如实报告自己解决方案是其帕累托最优
3. 平台信息披露策略：
   1. naive workers：始终公布一个与实际值无关的较高的平均准确率
   2. strategic workers：有动机公布一个低于实际值的平均准确率
4. 性能评估：数值实验
   1. 平台报酬增加了workers对高准确率workers数量的先验信念
   2. 平台报酬可能会减小高准确率workers的数量

## Related Work

### Information Elicitation Without Verification(IEWV)

1. 设计适当的激励
2. 同伴预测
3. 大都假设信息对称

### Strategic Information Revelation

1. 不完全信息中的cheap talk问题——假设平台不会说谎
2. 考虑信息获取和揭示代价的劝说博弈——认为信息披露是唯一的决策

## Model

### Workers' Decisions and Payoffs

1. 任务和workers：
   1. 任务：二值问题，例如判断数学问题求解方案是否正确，解空间`{1， -1}`，分别表示正确和错误。
   2. workers：$N$个，$x_i^{estimate}$表示第$i$个worker对该任务解的估计值，$x_i^{report}$表示第$i$个worker对该任务解的上报值，这二者可能一样也可能不同。
2. workers的努力策略：
   1. worker可以决定是否努力工作，求解准确性和他选择的努力程度有关，努力程度为`{0,1}`两种。
      1. 努力程度为0时，worker求解准确率为0.5，代价为0，且无法获取关于任务解方案的任何信息（即其估计值为真或假的概率相等）
      2. 努力程度为1时，其求解准确率会提高为$p_i$，代价为$c$。
   2. workers的异构：$N$个workers中有$k$个高准确率的worker（准确率为$p_h$）使用集合$N_h$表示；$N-k$个低准确率的worker（准确率为$p_l$），使用集合$N_l$表示。两个准确率值均大于0.5小于1。
3. worker的上报策略：
   1. 对于不努力的worker：策略唯一，随机上报，用`rd`表示
   2. 对于努力的worker：策略空间`{1,-1}`，分别表示如实报告和谎报
   3. workers可以共谋，大家都报告1或者-1，但对于在线众包系统，这样的共谋并不现实；平台检测到共谋则会删除该worker，因此认为workers的报告策略是独立的，且只有`{rd,1,-1}`这三个选项。
   4. $s_i=(e_i,r_i)$表示第$i$个worker的努力策略和报告策略，具体来说$s_i\in S_i={(0,rd),(1,1),(1,-1)}$
4. 一致性奖励：
   1. 报告值和大多数人一样的worker可以得到奖励$R$。
   2. $G_i(s;\epsilon)$表示第$i$个worker收到$R$的概率，其中，$s=(e_i,r_i)$，$\epsilon$是平台策略，在后面进行分析。
5. worker的收益：$u_i(s;\epsilon,R)=G_i(s;\epsilon)\cdot R-e_i\cdot c$

### Platform’s Decisions and Payoff

1. 平台的信息披露策略：

   1. 平台拥有的信息优势：workers求解准确率的分布，也就是高准确率workers的数量$k$

   2. 平台和workers之间非对称信息披露的贝叶斯说服框架：

      1. 平台和workers都不知道$k$，平台需要进行长期信息披露策略：

         1. 平台虽然不知道$k$，但是知道$k$的分布，从而得到先验信念$\mu^{prior}=(\mu_{high}^{prior},\mu_{low}^{prior})$，其中$\mu_{high}^{prior}=Pr(k=k^{high})$，$\mu_{low}^{prior}=Pr(k=k^{low})$。$\mu_{high}^{prior}+\mu_{low}^{prior}=1$，且$k^{high}$和$k^{low}$是$k$的两个可能取值。

         2. 注意，出于简化，上述公式中$k$的分布是两点分布，本方法同样适用于其他分布的情况。

         3. 在$k$被发现之前，平台会预定一个信息披露策略（？是否公开）。

         4. 平台可以有多个任务，每个任务有各自对应的$k$，在workers到来之前（也即是在$k$被发现之前），平台会先决定信息披露策略，并承诺会按这个策略执行，从而建立良好信誉。

         5. $\epsilon = (\epsilon^h, \epsilon^l)\in [0,1]^2$：平台的信息披露策略。假设当$k=k^{low}$时，平台宣称$k_p^{anu}=k^{high}$的概率是$\epsilon^h$，而反之，当$k=k^{high}$时，平台宣称$k_p^{anu}=k^{low}$的概率是$\epsilon^l$。

         6. 总结一下就是平台会按下列概率决策：

            $Pr(k_p^{anu}=k^{high}|k=k^{low})=\epsilon^h$

            $Pr(k_p^{anu}=k^{low}|k=k^{low})=1-\epsilon^h$

            $Pr(k_p^{anu}=k^{high}|k=k^{high})=1-\epsilon^l$

            $Pr(k_p^{anu}=k^{low}|k=k^{high})=\epsilon^l$
         
            即：k有两个取值，一个high，一个low，当真实的k是high时，平台说谎的概率是$\epsilon^l$；当真实的k是low时，平台说谎的概率是$\epsilon^h$。

      2. workers执行任务，平台观察到$k$，workers尚且不知道；

      3. 平台依据之前决定的策略公开$k_p^{anu}$，可能和真实$k$不同，平台决定的$\epsilon$会影响workers对$k$的后验信念，从而影响平台的信誉和收益。
      
      4. workers可以通过重复与平台交互从而了解平台的信息披露策略，也可以通过平台反馈以及信誉系统来了解信息披露策略。

2. 平台的奖励设计策略：$R$，决定worker均衡收益

3. 平台的收益：准确率和代价之间的均衡

   $U_p(\epsilon,R,k;s)=\beta P_a(\epsilon,R,k;s)-E\{R^{tot}(\epsilon,R,k;s)\}$

   1. $P_a(\epsilon,R,k;s)$表示workers的任务聚合准确率，使用少数服从多数的规则来计算该概率。
   2. $\beta$表示平台对聚合准确率的估值
   3. $E\{R^{tot}(\epsilon,R,k;s)\}$表示对总的一致性奖励的预期支出（这里的$R^{tot}$是指什么？）

## SOLVING THREE-STAGE MODEL

本章节使用逆向归纳法，仅分析strategic worker。

### Worker Equilibrium Behaviors in Stage III

1. 前提：给定平台策略$\epsilon$和$R$，每个worker选择自己的努力程度和报告策略$s_i$来最大化自身收益

2. worker的信念更新：平台公布$k_p^{anu}$之前，workers有对$k$的先验信念$\mu^{prior}$（是每人一个还是所有worker共用？）

   2. 平台公布$k_p^{anu}$后，workers基于先验信念和平台公布值更新后验信念$\mu_w^{post,str}|k_p^{anu}$，其中$w\in \{high,low\}$。计算如下：
      $$
      \mu_{high}^{post,str}|k^{high}(\epsilon)=\frac{(1-\epsilon^l)\mu_{high}^{prior}}{(1-\epsilon^l)\mu_{high}^{prior}+\epsilon^h\mu_{low}^{prior}}
      $$

      $$
      \mu_{low}^{post,str}|k^{high}(\epsilon)=\frac{\epsilon^h\mu_{low}^{prior}}{(1-\epsilon^l)\mu_{high}^{prior}+\epsilon^h\mu_{low}^{prior}}
      $$

      $$
      \mu_{high}^{post,str}|k^{low}(\epsilon)=\frac{\epsilon^l\mu_{low}^{prior}}{\epsilon^l\mu_{high}^{prior}+(1-\epsilon^h)\mu_{low}^{prior}}
      $$

      $$
      \mu_{low}^{post,str}|k^{low}(\epsilon)=\frac{(1-\epsilon^h)\mu_{low}^{prior}}{\epsilon^l\mu_{high}^{prior}+(1-\epsilon^h)\mu_{low}^{prior}}
      $$

      推导过程用到了[贝叶斯公式](https://zhuanlan.zhihu.com/p/134036707)。

   3. 显然，第一个公式里，随着$\epsilon^h$的增加，workers对k为high的后验信念逐渐减小，也就是说，如果平台在k实际为low时说谎的概率增加，则workers在听到平台说k为high时，会怀疑平台说谎；类似地，在第四个公式中，随着$\epsilon^l$的增加，workers对k为low的后验信念逐渐减小，也就是说，如果平台在k实际为high时说谎的概率增加，则workers在听到平台说k为low时，会怀疑平台，进而减小后验信念。

3. worker的均衡策略：worker根据后验信念做出是否努力以及是否如实汇报的决策，本文关注对称纳什均衡——相同类型（任务求解准确率）的worker会有相同的决策。

   1. 定义1：
   
      1. $n-SNE$：$(s_i^*=(0,rd), \forall i\in N)$，没有worker会努力和如实报告
      2. $f-SNE$：$(s_i^*=(1,1), \forall i\in N)$， 所有worker都努力和如实报告
      3. $p-SNE$：$(s_i^*=(1,1), \forall i\in N_h, s_j^*=(0,rd), \forall j\in N_l)$，高准确率的worker会努力和如实报告，低准确率的worker会不努力和随机报告
   
   2. 定理1：
   
      1. 给定任意$\epsilon\in [0,1]^2$，$R\geq 0$时一定存在一个$n-SNE$.
      2. 给定任意$\epsilon\in [0,1]^2$，始终存在阈值$R_f^{str}(\epsilon, k_p^{anu})> 0$，使得当且仅当$R>R_f^{str}(\epsilon,k_p^{anu})$时存在$f-SNE$.
      3. 当$\epsilon\in \Phi=\{\epsilon\in [0,1]^2|condition (11) 成立\}$时，存在两个阈值$0<R_{pl}^{str}(\epsilon,k_p^{anu})\leq R_{ph}^{str}(\epsilon, k_p^{anu})$，使得当且仅当$R_{pl}^{str}(\epsilon,k_p^{anu})\leq R \leq R_{ph}^{str}(\epsilon, k_p^{anu})$时，存在$p-SNE$. $condition (11)$如下：
   
      $$
      \frac{2p_h-1}{2p_l-1}(\mu_{high}^{post,str}|k_p^{anu}(\epsilon)P_{k^{high}-1}^{majority}+\mu_{low}^{post,str}|k_p^{anu}(\epsilon)P_{k^{low}-1}^{majority})\geq \mu_{high}^{post,str}|k_p^{anu}(\epsilon)P_{k^{high}}^{majority}+\mu_{low}^{post,str}|k_p^{anu}(\epsilon)P_{k^{low}}^{majority}
      $$
   
      ​		在该公式中，$P_{k^{high}-1}^{majority}$是指：当$k^{high}-1$个高准确率workers决定采取策略$(1,1)$且剩下的那个高准确率workers决定采取策略$(0,rd)$时，$N-1$个workers的解决方案中的大多数是正确的概率（也就是多数一致方案是正确的概率）
   
      
   
   3. 推论1：
   
   4. 定理2：

### Platform Reward Design in Stage II

### Platform Information Revelation in Stage I

## NUMERICAL RESULTS

### Impact of Worker Characteristics

### Impact of Worker Prior Belief

## CONCLUSION

1. 策略性信息披露问题——非凸规划
2. naive workers：总是公开一个较高的平均准确率
3. strategic workers：收益和平台信用的平衡，有动机宣布一个低于实际值的平均准确率
4. 平台报酬可能减少高准确率workers的数量
5. 未来工作：
   1. 多维workers异质性
   2. 考虑信息披露的代价（获取信息所产生的成本）

## Reference

1. [贝叶斯公式](https://zhuanlan.zhihu.com/p/134036707)