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
   4. $s_i=(e_i,r_i)$表示第$i$个worker的努力策略和报告策略，具体来说$s_i\in S_i=\{(0,rd),(1,1),(1,-1)\}$
4. 一致性奖励：
   1. 报告值和大多数人一样的worker可以得到奖励$R$。
   2. $G_i(s;\epsilon)$表示第$i$个worker收到$R$的概率，其中，$s=(e_i,r_i)$，$\epsilon$是平台策略，在后面进行分析。
5. worker的收益：$u_i(s;\epsilon,R)=G_i(s;\epsilon)\cdot R-e_i\cdot c$

### Platform's Decisions and Payoff

1. 平台的信息披露策略：
   
   1. 平台拥有的信息优势：workers求解准确率的分布，也就是高准确率workers的数量$k$
   
   2. 平台和workers之间非对称信息披露的贝叶斯说服框架：
      
      1. 平台和workers都不知道$k$，平台需要进行长期信息披露策略：
         
         1. 平台虽然不知道$k$，但是知道$k$的分布，从而得到先验信念$\mu^{prior}=(\mu_{high}^{prior},\mu_{low}^{prior})$，其中$\mu_{high}^{prior}=Pr(k=k^{high})$，$\mu_{low}^{prior}=Pr(k=k^{low})$。$\mu_{high}^{prior}+\mu_{low}^{prior}=1$，且$k^{high}$和$k^{low}$是$k$的两个可能取值。
         
         2. 这个先验同时也是workers的，大家都一样。
         
         3. 注意，出于简化，上述公式中$k$的分布是两点分布，本方法同样适用于其他分布的情况。
         
         4. 在$k$被发现之前，平台会预定一个信息披露策略（？是否公开——从后文看是公开的，或者说是workers能发现其规律）。
         
         5. 平台可以有多个任务，每个任务有各自对应的$k$，在workers到来之前（也即是在$k$被发现之前），平台会先决定信息披露策略，并承诺会按这个策略执行，从而建立良好信誉。
         
         6. $\epsilon = (\epsilon^h, \epsilon^l)\in [0,1]^2$：平台的信息披露策略。假设当$k=k^{low}$时，平台宣称$k_p^{anu}=k^{high}$的概率是$\epsilon^h$，而反之，当$k=k^{high}$时，平台宣称$k_p^{anu}=k^{low}$的概率是$\epsilon^l$。
         
         7. 总结一下就是平台会按下列概率决策：
            
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
   2. $\beta$表示平台对聚合准确率的估值（从公式上理解感觉更像是说当聚合结果是正确的时候所得到的收益）
   3. $E\{R^{tot}(\epsilon,R,k;s)\}$表示对总的一致性奖励的预期支出（这里的$R^{tot}$是指什么？）

## SOLVING THREE-STAGE MODEL

本章节使用逆向归纳法，仅分析strategic worker。

### Worker Equilibrium Behaviors in Stage III

1. 前提：给定平台策略$\epsilon$和$R$，每个worker选择自己的努力程度和报告策略$s_i$来最大化自身收益

2. worker的信念更新：平台公布$k_p^{anu}$之前，workers有对$k$的先验信念$\mu^{prior}$（是每人一个还是所有worker共用？）
   
   1. 平台公布$k_p^{anu}$后，workers基于先验信念和平台公布值更新后验信念$\mu_w^{post,str}|k_p^{anu}$，其中$w\in \{high,low\}$。计算如下：
      
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
      
      前两行表示平台宣布$k=k^{high}$时，worker对$k$实际值的后验信念；后两行表示平台宣布$k=k^{low}$时，worker对$k$实际值的后验信念。
   
   2. 显然，第一个公式里，随着$\epsilon^h$的增加，workers对k为high的后验信念逐渐减小，也就是说，如果平台在k实际为low时说谎的概率增加，则workers在听到平台说k为high时，会怀疑平台说谎；类似地，在第四个公式中，随着$\epsilon^l$的增加，workers对k为low的后验信念逐渐减小，也就是说，如果平台在k实际为high时说谎的概率增加，则workers在听到平台说k为low时，会怀疑平台，进而减小后验信念。

3. worker的均衡策略：worker根据后验信念做出是否努力以及是否如实汇报的决策，本文关注对称纳什均衡——相同类型（任务求解准确率）的worker会有相同的决策。
   
   1. 定义1：
      
      1. $n-SNE$：$(s_i^*=(0,rd), \forall i\in N)$，没有worker会努力和如实报告
      2. $f-SNE$：$(s_i^*=(1,1), \forall i\in N)$， 所有worker都努力和如实报告
      3. $p-SNE$：$(s_i^*=(1,1), \forall i\in N_h, s_j^*=(0,rd), \forall j\in N_l)$，高准确率的worker会努力和如实报告，低准确率的worker会不努力和随机报告
   
   2. 定理1：
      
      1. 给定任意$\epsilon\in [0,1]^2$，$R\geq 0$时一定存在一个$n-SNE$.
      2. 给定任意$\epsilon\in [0,1]^2$，始终存在阈值$R_f^{str}(\epsilon, k_p^{anu})> 0$，使得当且仅当$R>R_f^{str}(\epsilon,k_p^{anu})$时存在$f-SNE$.
      3. 当$\epsilon\in \Phi=\{\epsilon\in [0,1]^2|condition (11) \}$成立时，存在两个阈值$0<R_{pl}^{str}(\epsilon,k_p^{anu})\leq R_{ph}^{str}(\epsilon, k_p^{anu})$，使得当且仅当$R_{pl}^{str}(\epsilon,k_p^{anu})\leq R \leq R_{ph}^{str}(\epsilon, k_p^{anu})$时，存在$p-SNE$. $condition (11)$如下：
      
      $$
      \frac{2p_h-1}{2p_l-1}(\mu_{high}^{post,str}|k_p^{anu}(\epsilon)P_{k^{high}-1}^{majority}+\mu_{low}^{post,str}|k_p^{anu}(\epsilon)P_{k^{low}-1}^{majority})\geq \mu_{high}^{post,str}|k_p^{anu}(\epsilon)P_{k^{high}}^{majority}+\mu_{low}^{post,str}|k_p^{anu}(\epsilon)P_{k^{low}}^{majority} \tag{11}
      $$
      
      ​        在该公式中，$P_{k^{high}-1}^{majority}$是指：当$k^{high}-1$个高准确率workers决定采取策略$(1,1)$且剩下的其他workers都决定采取策略$(0,rd)$时，$N-1$个workers的解决方案中的大多数是正确的概率（也就是多数一致方案是正确的概率）
      
      ​        $P_{k^{low}-1}^{majority}$是指：当$k^{low}-1$个高准确率workers决定采取策略$(1,1)$且剩下的其他workers决定采取策略$(0,rd)$时，$N-1$个workers的解决方案中的大多数是正确的概率（也就是多数一致方案是正确的概率）
      
      ​        $P_{k^{high}}^{majority}$是指：$k^{high}$个高准确率worker决定采取策略$(1,1)$，剩下的其他workers决定采取策略$(0,rd)$时，$N-1$个workers的解决方案中的大多数是正确的概率（也就是多数一致方案是正确的概率）
      
      ​        $P_{k^{low}}^{majority}$是指：$k^{low}$个高准确率worker决定采取策略$(1,1)$，剩下的其他workers决定采取策略$(0,rd)$时，$N-1$个workers的解决方案中的大多数是正确的概率（也就是多数一致方案是正确的概率）
      
      ​        这个公式算起来很复杂，它表达的场景是：当选择努力时，高准确率worker相信他们更有可能拿到奖励（而不是低准确率worker），也就是说高准确率worker认为自己是大多数的那部分。反之，当这个公式不满足时，高准确率worker会觉得自己拿到奖励的概率很低，从而使得高准确率worker的期望收益很低。此时，高准确率worker不会努力，而是选择降低成本，进而不存在$p-SNE$。

4. 推论1：
   
   1. 对于一个固定的$\epsilon^l$，如果$k_p^{anu}=k^{high}$，则定理1中的$R_f^{str}(\epsilon,k_p^{anu})$和$R_{pl}^{str}(\epsilon,k_p^{anu})$随$\epsilon^h$增加而增加；反之，如果$k_p^{anu}=k^{low}$，则这两个都随$\epsilon^h$增加而减小。
      
      说明：如果平台更喜欢把$k_p^{anu}$谎报成$k^{high}$（也就是说在$k^{low}$时说谎），那么为了达成$f-SNE$和$p-SNE$，平台需要提供更高报酬（也就是更大的$R$）。分析原因：这种情况会让workers觉得实际的$k$并不是$k^{high}$而是$k^{low}$，也就是说$\mu_{high}^{post,str}|k^{high}(\epsilon)$会减小，因此通过多数一致获得的奖励会减少，而为了激励workers，平台就需要提高奖励，从而提高workers的期望收益。
   
   2. 对于一个固定的$\epsilon^h$，如果$k_p^{anu}=k^{high}$，则定理1中的$R_f^{str}(\epsilon,k_p^{anu})$和$R_{pl}^{str}(\epsilon,k_p^{anu})$随$\epsilon^l$增加而增加；反之，如果$k_p^{anu}=k^{low}$，则这两个都随$\epsilon^l$增加而减小。
      
      说明：如果平台更喜欢把$k_p^{anu}$谎报成$k^{low}$（也就是说在$k^{high}$时说谎），那么为了达成$f-SNE$和$p-SNE$，平台也一样需要提高报酬来激励。这里的推导可以看前面workers更新后验信念那里的公式来理解。
   
   3. 对于适当的$\epsilon$和$R$，定理1中的几个不同的$SNE$可以共存。

5. 定理2：帕累托最优：对于任意给定$\epsilon$和$R$，workers之间存在一个帕累托最优均衡解。
   
   本文假设当多个均衡解共存时，workers会选择帕累托最优。

### Platform Reward Design in Stage II

这一部分分析对于给定的$\epsilon$，平台观察$k$并决定$R$，从而在第三阶段达到帕累托最优。

1. 定义2：$z\in \{n,f,p\}$表示均衡解索引
   
   1. $P_z(k)$：均衡$z-SNE$的任务聚合准确率
   
   2. $E\{R_z^{tot}(\epsilon,k,k_p^{anu})\}$：均衡$z-SNE$的总期望一致性奖励
   
   3. $B_z(\epsilon,k,k_p^{anu})$：对于每单位$R$，均衡解从$n-SNE$提升到$z-SNE$所带来的平均准确率的提升，计算方式如下：
      
      $$
      B_z(\epsilon,k,k_p^{anu})=\frac{P_z(k)-P_n(k)}{E\{R_z^{tot}(\epsilon,k,k_p^{anu})\}}
      $$

2. 定理3：
   
   1. 如果$(11)$成立且$B_p(\epsilon,k,k_p^{anu})>B_f(\epsilon,k,k_p^{anu})$，则平台的最优奖励为：
      
      $$
      R^*=\left\{
\begin{aligned}
&0, &if \ \ \beta<\frac{1}{B_p(\epsilon,k,k_p^{anu})}\\
&R_{pl}^{str}(\epsilon,k_p^{anu}), &if\ \ \frac{1}{B_p(\epsilon,k,k_p^{anu})} \leq \beta<\widetilde\beta(\epsilon,k,k_p^{anu}) \\
&R_{f}^{str}(\epsilon,k_p^{anu}), &if\ \ \beta\geq \widetilde\beta(\epsilon,k,k_p^{anu})
\end{aligned}
\right.
      $$
      
      其中，$\widetilde\beta(\epsilon,k,k_p^{anu})=\frac{E\{R_f^{tot}(\epsilon,k,k_p^{anu})-E\{R_p^{tot}(\epsilon,k,k_p^{anu})}{P_f(k)-P_p(k)}$
   
   2. 如果$(11)$不成立或者$B_p(\epsilon,k,k_p^{anu})<B_f(\epsilon,k,k_p^{anu})$，则平台的最优奖励为：
      
      $$
      R^*=\left\{
\begin{aligned}
&0, &if \ \ \beta<\frac{1}{B_f(\epsilon,k,k_p^{anu})}\\
&R_{f}^{str}(\epsilon,k_p^{anu}), &if\ \ \beta\geq \frac{1}{B_f(\epsilon,k,k_p^{anu})}
\end{aligned}
\right.
      $$
      
      注意，$(11)$是达到$p-SNE$所必须的条件。

3. 定理3说明了：
   
   1. 如果$p-SNE$存在，且它比$f-SNE$的单位收益准确率提升更高，且平台的估值$\beta$是温和的，平台会通过选择$R^*=R_{pl}^{str}(\epsilon,k_p^{anu})$引出$p-SNE$作为第三阶段的帕累托均衡来最大化自己的收益。
   2. 如果$p-SNE$不存在，或者它比$f-SNE$的单位收益准确率提升低，那么$p-SNE$对平台而言就不是最好的均衡解。当$\beta$很大时，平台会通过选择$R^*=R_f^{str}(\epsilon,k_p^{anu})$引出$f-SNE$作为第三阶段的帕累托最优。

### Platform Information Revelation in Stage I

这一部分讨论平台的信息披露策略。在该阶段中，平台决定自己的信息披露策略$\epsilon = (\epsilon^h,\epsilon^l)\in[0,1]^2$，并预测自己在第二阶段中的$R$和workers在第三阶段中的帕累托均衡。

重复一下前文的符号表示：

> $\epsilon = (\epsilon^h, \epsilon^l)\in [0,1]^2$：平台的信息披露策略。假设当$k=k^{low}$时，平台宣称$k_p^{anu}=k^{high}$的概率是$\epsilon^h$，而反之，当$k=k^{high}$时，平台宣称$k_p^{anu}=k^{low}$的概率是$\epsilon^l$。
> 
> 总结一下就是平台会按下列概率决策：
> 
> $Pr(k_p^{anu}=k^{high}|k=k^{low})=\epsilon^h$
> 
> $Pr(k_p^{anu}=k^{low}|k=k^{low})=1-\epsilon^h$
> 
> $Pr(k_p^{anu}=k^{high}|k=k^{high})=1-\epsilon^l$
> 
> $Pr(k_p^{anu}=k^{low}|k=k^{high})=\epsilon^l$
> 
> 即：k有两个取值，一个high，一个low，当真实的k是high时，平台说谎的概率是$\epsilon^l$；当真实的k是low时，平台说谎的概率是$\epsilon^h$。

1. 定理4：对平台而言，设置为$\epsilon^h=1,\epsilon^l=0$并不总是最优解。
   
   换言之，宣称$k_p^{anu}=k^{high}$并不总是最好的，具体分析如下：
   
   首先，复习内容：$k$的真实值影响第2阶段中的奖励设计，而平台对$k$的宣称值$k_p^{anu}$影响第3阶段中workers的行为。考虑一下4种情况：
   
   1. Case $(h,h)$: $k=k^{high}$ 且 $k_p^{anu}=k^{high}$，出现概率$Q_{h,h}(\epsilon)=\mu_{high}^{prior}(1-\epsilon^l)$
   2. Case $(h,l)$: $k=k^{high}$ 且 $k_p^{anu}=k^{low}$，出现概率$Q_{h,l}(\epsilon)=\mu_{high}^{prior}\epsilon^l$
   3. Case $(l,h)$: $k=k^{low}$ 且 $k_p^{anu}=k^{high}$，出现概率$Q_{l,h}(\epsilon)=\mu_{low}^{prior}\epsilon^h$
   4. Case $(l,l)$: $k=k^{low}$ 且 $k_p^{anu}=k^{low}$，出现概率$Q_{l,l}(\epsilon)=\mu_{low}^{prior}(1-\epsilon^h)$
   
   固定$\epsilon^l$，平台的期望收益为：
   
   $$
   E\{U_p(\epsilon^h)\}=Q_{h,h}(\epsilon^h)U_{h,h}(\epsilon^h)+Q_{h,l}(\epsilon^h)U_{h,l}(\epsilon^h)+Q_{l,h}(\epsilon^h)U_{l,h}(\epsilon^h)+Q_{l,l}(\epsilon^h)U_{l,l}(\epsilon^h)
   $$
   
   $U_{h,h}$表示在Case $(h,h)$的情况下，平台在第2阶段最优化奖励值后的最大收益。其他几个U也是类似的含义。
   
   接下来分析期望收益随披露策略的变化趋势。

2. 引理1：
   
   1. $U_{h,h}(\epsilon^h),U_{l,h}(\epsilon^h),Q_{l,l}(\epsilon^h)$随$\epsilon^h$增加而减小。
   2. $U_{h,l}(\epsilon^h),U_{l,l}(\epsilon^h),Q_{l,h}(\epsilon^h)$随$\epsilon^h$增加而增加。
   
   分析：$Q_{l,l}(\epsilon^h)$和$Q_{l,h}(\epsilon^h)$随$\epsilon^h$的变化从定义即可看出。接下来用Case $(h,h)$ 中的 $U_{h,h}(\epsilon^h)$作为例子来分析。在推论1中可知，随$\epsilon^h$增加，平台需要支付更大的奖励$R$来激励workers，而这会减小平台的收益。其他几个U也是类似的分析思路。
   
   而由于$E\{U_p(\epsilon^h)\}$的几部分单调性不同，因此无法直接分析出平台收益随$\epsilon^h$的变化趋势。同样的，$E\{U_p(\epsilon^l)\}$也无法分析变化趋势。从而得出定理4的结论。

具体的平台披露策略因为太复杂了，所以没法分析，在实验部分进行了数值实验。

## NUMERICAL RESULTS

这一部分进行数值实验，研究两类workers：策略型和天真型，天真型的符号表达沿用前文中策略型的，只是把str改成了nai，具体如下：

$$
\left\{
\begin{aligned}
\mu_{high}^{post,nai}|k^{high}=\mu_{low}^{post,nai}|k^{low}=1\\
\mu_{high}^{post,nai}|k^{low}=\mu_{low}^{post,nai}|k^{high}=0\\
\end{aligned}
\right.
$$

接下来的实验结果说明：平台始终向天真的工人宣布高平均工人准确率是最佳的（也就是$k=k^{high}$），但对策略型工人来说并非如此。我们还显示了一个反直觉的结果，表明平台的收益随高准确率workers的准确率提高而提高，随高准确率workers的数量提高而减小。

### Impact of Worker Characteristics

这一部分研究高准确率workers的准确率$p_h$对平台最优收益、workers总收益（所有workers的收益和）和社会福利（平台收益+workers收益）的影响。

参数：workers数量$N=100$，高准确率workers的准确率$p_h\in(0.7,0.8),step=0.02$，低准确率workers的准确率$p_l=0.6$，对$k$的先验信念$\mu_{high}^{prior}=0.7,\mu_{low}^{prior}=0.3$，$k$的取值$k^{low}=20,k^{high}\in\{50,70\}$，workers努力的成本$c=1$，平台对聚合准确的估值$\beta=1000$。

分析图像：

1. 平台收益随$p_h$增加而增加，对于某些$p_h$随$k^{high}$增加（指从50变成70）而减小。
2. 天真的workers给平台带来的收益更高。
3. workers收益可能随$p_h$增加而减小。
4. 社会财富随$p_h$增加而增加。

### Impact of Worker Prior Belief

这一部分研究先验信念对平台最优收益、workers总收益和平台披露策略的影响。

参数：workers数量$N=100$，高准确率workers的准确率$p_h=0.75$，低准确率workers的准确率$p_l=0.6$，对$k$的先验信念$\mu_{high}^{prior}\in\{0.01,0.2,0.4,0.6,0.8,0.99\}$，$k$的取值$k^{low}=20,k^{high}\in\{50,70\}$，workers努力的成本$c=1$，平台对聚合准确的估值$\beta=1000$。

分析图像：

1. 平台收益随先验$\mu_{high}^{prior}$增加而增加。
2. 策略型workers的聚合收益随先验$\mu_{high}^{prior}$增加而减小，天真型workers的聚合收益与先验$\mu_{high}^{prior}$无关。
3. 面对天真型workers，平台始终宣称$k=k^{high}$是最优的；面对策略型workers则不是这样。
4. 面对策略型workers时，平台的最优$\epsilon^{h}$随先验$\mu_{high}^{prior}$增加而减小，最优$\epsilon^{l}$随先验$\mu_{high}^{prior}$增加而增加。

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

## 我的分析

### 第三阶段workers均衡解定理1

要用到的字母表达：

$P_{k^{high}-1}^{majority}$是指：当$k^{high}-1$个高准确率workers决定采取策略$(1,1)$且剩下的其他workers都决定采取策略$(0,rd)$时，$N-1$个workers的解决方案中的大多数是正确的概率（也就是多数一致方案是正确的概率）

$P_{k^{low}-1}^{majority}$是指：当$k^{low}-1$个高准确率workers决定采取策略$(1,1)$且剩下的其他workers决定采取策略$(0,rd)$时，$N-1$个workers的解决方案中的大多数是正确的概率（也就是多数一致方案是正确的概率）

$P_{k^{high}}^{majority}$是指：$k^{high}$个高准确率worker决定采取策略$(1,1)$，剩下的其他workers决定采取策略$(0,rd)$时，$N-1$个workers的解决方案中的大多数是正确的概率（也就是多数一致方案是正确的概率）

$P_{k^{low}}^{majority}$是指：$k^{low}$个高准确率worker决定采取策略$(1,1)$，剩下的其他workers决定采取策略$(0,rd)$时，$N-1$个workers的解决方案中的大多数是正确的概率（也就是多数一致方案是正确的概率）

$\mu_{high}^{post,str}|k_p^{anu}(\epsilon)$和$\mu_{low}^{post,str}|k_p^{anu}(\epsilon)$是高质量workers的数量的后验概率

我增加的字母表达：$P^{00}$表示高低质量都采取$(0,rd)$时多数一致方案是正确的概率，$P^{01}$表示高质量采取$(0,rd)$低质量采取$(1,-1)$...类似的，可以把workers策略组合的所有概率表达都写出来，上标左边的数字表示高质量workers的策略，右边的数字表示低质量workers的策略，数字012分别表示策略$(0,rd),(1,-1),(1,1)$。

显然，$P^{00}=0.5$，$P^{20}=\mu_{high}^{post,str}|k_p^{anu}(\epsilon)P_{k^{high}}^{majority}+\mu_{low}^{post,str}|k_p^{anu}(\epsilon)P_{k^{low}}^{majority}$

本文假设相同质量的workers会采取相同的策略，也就是说，高低质量的workers的策略组合一共有9种，我们列出收益矩阵：

|        | (0,rd)         | (1,-1) | (1,1) |
| ------ | -------------- | ------ | ----- |
| (0,rd) | $(0.5R, 0.5R)$ |        |       |
| (1,-1) |                |        |       |
| (1,1)  |                |        |       |

#### 低质量workers选$(0,rd)$

1. 高质量workers选$(0,rd)$，所有workers的答案是正确和错误的概率都是0.5，因此大家的收益都是$0.5R$，没有支出。
2. 高质量workers选$(1,1)$，高质量workers正确的概率是$p_h$，低质量workers正确的概率是0.5，多数一致方案正确的概率是$\mu_{high}^{post,str}|k_p^{anu}(\epsilon)P_{k^{high}}^{majority}+\mu_{low}^{post,str}|k_p^{anu}(\epsilon)P_{k^{low}}^{majority}$，
3. 高质量workers选$(1,-1)$，高质量workers正确的概率是$1-p_h$，低质量workers正确的概率是0.5，多数一致方案正确的概率是$P^{10}$。

#### 低质量workers选$(1,-1)$

1. 高质量workers选$(0,rd)$，高质量workers正确的概率是0.5，低质量workers正确的概率是$1-p_l$，多数一致方案正确的概率是$P^{01}$。
2. 高质量workers选$(1,1)$，高质量workers正确的概率是$p_h$，低质量workers正确的概率是$1-p_l$，多数一致方案正确的概率是$P^{21}$。
3. 高质量workers选$(1,-1)$，高质量workers正确的概率是$1-p_h$，低质量workers正确的概率是$1-p_l$，多数一致方案正确的概率是$P^{11}$。

#### 低质量workders选$(1,1)$

1. 高质量workers选$(0,rd)$，高质量workers正确的概率是0.5，低质量workers正确的概率是$p_l$，多数一致方案正确的概率是$P^{02}$。
2. 高质量workers选$(1,1)$，高质量workers正确的概率是$p_h$，低质量workers正确的概率是$p_l$，多数一致方案正确的概率是$P^{22}$。
3. 高质量workers选$(1,-1)$，高质量workers正确的概率是$1-p_h$，低质量workers正确的概率是$p_l$，多数一致方案正确的概率是$P^{12}$。

这里看起来是把每一个都列出来，然后比较大小找均衡解。

#### $Condition(11)$的推导

在$p-SNE$的情况下，高准确率的worker采取策略$(1,1)$，低准确率的worker采取策略$(0,rd)$。

对于一个高质量worker，该workers努力时得到正确答案概率：$p_h$，得到错误答案概率：$1-p_h$。

其他workers中，高质量workers的数量$k$为$k^{high}-1$或者$k^{low}-1$，且对应的概率分别是两个后验概率$\mu_{high}^{post,str}|k_p^{anu}(\epsilon)$和$\mu_{low}^{post,str}|k_p^{anu}(\epsilon)$，而这两种情况下其他workers的多数一致方案是正确和错误的概率分别是$P_{k^{high}-1}^{majority}$和$P_{k^{low}-1}^{majority}$，即可得：

其他workers的多数一致方案是正确的概率：$\mu_{high}^{post,str}|k_p^{anu}(\epsilon)P_{k^{high}-1}^{majority}+\mu_{low}^{post,str}|k_p^{anu}(\epsilon)P_{k^{low}-1}^{majority}$，

其他workers的多数一致方案是错误的概率：$\mu_{high}^{post,str}|k_p^{anu}(\epsilon)(1-P_{k^{high}-1}^{majority})+\mu_{low}^{post,str}|k_p^{anu}(\epsilon)(1-P_{k^{low}-1}^{majority})$

一个努力且如实报告的高质量worker得到收益的概率为：

$$
p_h(\mu_{high}^{post,str}|k_p^{anu}(\epsilon)P_{k^{high}-1}^{majority}+\mu_{low}^{post,str}|k_p^{anu}(\epsilon)P_{k^{low}-1}^{majority})+(1-p_h)(\mu_{high}^{post,str}|k_p^{anu}(\epsilon)(1-P_{k^{high}-1}^{majority})+\mu_{low}^{post,str}|k_p^{anu}(\epsilon)(1-P_{k^{low}-1}^{majority})) \tag{1}
$$

对于一个低质量worker而言，该workers努力时得到正确答案概率：$p_l$，得到错误答案概率：$1-p_l$，其他workers中，高质量workers的数量$k$为$k^{high}$或者$k^{low}$，且对应的概率分别是两个后验概率$\mu_{high}^{post,str}|k_p^{anu}(\epsilon)$和$\mu_{low}^{post,str}|k_p^{anu}(\epsilon)$，而这两种情况下其他workers的多数一致方案是正确和错误的概率分别是$P_{k^{high}}^{majority}$和$P_{k^{low}}^{majority}$，即可得：

其他workers的多数一致方案是正确的概率：$\mu_{high}^{post,str}|k_p^{anu}(\epsilon)P_{k^{high}}^{majority}+\mu_{low}^{post,str}|k_p^{anu}(\epsilon)P_{k^{low}}^{majority}$，

其他workers的多数一致方案是错误的概率：$\mu_{high}^{post,str}|k_p^{anu}(\epsilon)(1-P_{k^{high}}^{majority})+\mu_{low}^{post,str}|k_p^{anu}(\epsilon)(1-P_{k^{low}}^{majority})$

一个努力且如实报告的低质量worker得到收益的概率为：

$$
p_l(\mu_{high}^{post,str}|k_p^{anu}(\epsilon)P_{k^{high}}^{majority}+\mu_{low}^{post,str}|k_p^{anu}(\epsilon)P_{k^{low}}^{majority})+(1-p_l)(\mu_{high}^{post,str}|k_p^{anu}(\epsilon)(1-P_{k^{high}}^{majority})+\mu_{low}^{post,str}|k_p^{anu}(\epsilon)(1-P_{k^{low}}^{majority})) \tag{2}
$$

公式(1)大于公式(2)所推导出的公式和论文中的$condition(11)$不一样，暂时没想到其他的思路。

这里推导出的是：

$$
(1-p_h)(\mu_{high}^{post,str}|k_p^{anu}(\epsilon)+\mu_{low}^{post,str}|k_p^{anu}(\epsilon))+(2p_h-1)(\mu_{high}^{post,str}|k_p^{anu}(\epsilon)P_{k^{high}-1}^{majority}+\mu_{low}^{post,str}|k_p^{anu}(\epsilon)P_{k^{low}-1}^{majority})\geq \\
(1-p_l)(\mu_{high}^{post,str}|k_p^{anu}(\epsilon)+\mu_{low}^{post,str}|k_p^{anu}(\epsilon))+(2p_l-1)(\mu_{high}^{post,str}|k_p^{anu}(\epsilon)P_{k^{high}}^{majority}+\mu_{low}^{post,str}|k_p^{anu}(\epsilon)P_{k^{low}}^{majority})
$$

论文里的11是：

$$
\frac{2p_h-1}{2p_l-1}(\mu_{high}^{post,str}|k_p^{anu}(\epsilon)P_{k^{high}-1}^{majority}+\mu_{low}^{post,str}|k_p^{anu}(\epsilon)P_{k^{low}-1}^{majority})\geq \mu_{high}^{post,str}|k_p^{anu}(\epsilon)P_{k^{high}}^{majority}+\mu_{low}^{post,str}|k_p^{anu}(\epsilon)P_{k^{low}}^{majority} \tag{11}
$$

### 一些问题

1. 均衡解和阈值是怎么算出来的完全不懂
2. 实验部分是怎么算最优平台披露策略$\epsilon^h,\epsilon^l$
3. 图2(b)中增加的那段没有解释
4. $k^{high}=50$和$k^{high}=70$只有两个值是否能充分说明变化趋势（这个不重要）
5. 社会财富的增加是否与平台数值过大有关（这个也不重要）
6. 多数一致投票的时候，如果两边一样怎么处理（这个还不重要）