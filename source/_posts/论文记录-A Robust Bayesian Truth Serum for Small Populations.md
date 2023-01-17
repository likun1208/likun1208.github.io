---
title: 论文记录-A Robust Bayesian Truth Serum for Small Populations
date: 2023-1-2 15:57:40
tags:
    - game theory
    - trustworthiness
    - BTS
categories: 论文
description: 贝叶斯实话血清的扩展
---

# A Robust Bayesian Truth Serum for Small Populations

## Abstract
对等预测机制允许在真实的世界状态方面真实地引出私人信号（例如，经验或意见），而这个基础真相是不可观察的。最初的对等预测方法对于任意数量大于2的实体而言都是激励相容的，但依赖于所有实体共享的公共先验和机制。而贝叶斯吐真剂BTS则放宽了这个假设。BTS仍然假设代理共享同一个公共先验，但机制不需要知道该先验。然而，BTS仅在实体数量足够大的时候才激励相容，且需要的确切实体数量并不确定，而是取决于私有先验。本文提出了针对二值信息的稳定BTS，它对于任意实体数量大于3的场景都激励相容，利用二次函数评分规则的一个特性。RBTS是第一个针对$n \geq 3$的场景提供了严格激励相容而无需知道公共先验的对等预测机制。此外，与原始的BTS不同，我们的机制在数字上是稳健的，而且事后是个体理性的（*ex post* individually rational）。

## Intro
先略
## Related Work
先略
## The Setting
存在数量大于等于3的理性且风险中立的代理，其目标是最大化自己的期望收益。他们都共享同一个概率统计信念系统，该系统由两部分组成：状态和信号。状态$T$是一个随机遍历，取值$\lbrace 1,...,m\rbrace,m\geq 2$，代表了世界的真实状态。每一个实体$i$观察一个信号，记作随机变量$S_i$，是取值在$\lbrace 0,1 \rbrace$的二值变量，有时也记作$\lbrace l,h \rbrace$分别表示低和高。信号可以看作是代表了实体的经验或者意见。一个普通的信号用随机变量$S$表示。所有实体都有公共先验，由$Pr(T=t)$和$Pr(S=h|T=t)$组成，指给定任意可能状态$t$会观察到高级信号的条件概率。我们要求先验是可接受的：

**定义**1：如果公共先验满足以下属性，那么它就是可接受的：
1. 有两个或更多的状态，即$m\geq 2$。
2. 每一个状态都有正概率出现，即对于所有$t\in\lbrace 1,...,m \rbrace$来说，$Pr(T=t)>0$。
3. 状态之间是不同的，对于任意$t\neq t'$，都有$Pr(S=h|T=t)\neq Pr(S=h|T=t')$。所有状态都按惯例排序，即$Pr(S=h|T=1)<...<Pr(S=h|T=m)$。我们把这称为同种异构的特性。
4. 以状态为条件的信号信念是完全混合的，对于所有$t$，$0<Pr(S=h|T=t)<1$。

可接受性是一个弱要求。尤其要注意的是，任何先验都可以转化为可接受的先验，因为(1)对于具有正概率的状态，所有以状态为条件的信号信念是完全混合的；(2)对于至少两个具有正概率的状态，以状态为条件的信号信念是不同的。任何两个具有相同信号信念概率的状态都可以合并成一个新的状态，而概率为零的状态可以被放弃。该机制不需要任何关于可接受性以外的先验知识。

给定实体$i$的信号$s_i$，它可以更新自己关于另一个实体$j$会收到高级信号的概率的后验信念$P(S_j=h|S_i=s_i)$。因为公共先验，我们可以用$p_h=Pr(S_j=h|S_i=h)$和$p_l=Pr(S_j=h|S_i=l)$来分别表示一个普通实体在自己持有高和低信号的条件下对其他实体会持有高级信号的后验信念。我们将之称为一阶信号后验，且：
$$
p_{\{h\}}=\sum_{t=1}^m \operatorname{Pr}\left(S_j=h \mid T=t\right) \operatorname{Pr}\left(T=t \mid S_i=s_i\right)
$$
【个人理解】这个式子是指一个普通实体自己持有高信号的情况下，认为其他实体也持有高信号的概率。具体计算起来，就是自己持有信号$s_i$的情况下状态是$T=t$的条件概率，乘以状态是$T=t$的情况下，实体$j$持有高信号的概率，然后对所有可能的$t$求和，得到的总概率就是$p_h$。我其实觉得末尾的$S_i=s_i$应该是$S_i=h$，这样才能说得通。

其中关于状态的后验可以用通常的方式从贝叶斯规则中确定，等于
$$
\operatorname{Pr}\left(T=t \mid S_i=s_i\right)=\frac{\operatorname{Pr}\left(S_i=s_i \mid T=t\right) \operatorname{Pr}(T=t)}{\operatorname{Pr}\left(S_i=s_i\right)}
$$
而分母则是：
$$
\operatorname{Pr}\left(S_i=s_i\right)=\sum_{t=1}^m \operatorname{Pr}\left(S_i=s_i \mid T=t\right) \operatorname{Pr}(T=t)
$$
这些信号后验可以在实体知道两个信号的情况下进行类似的计算。我们扩展了这个符号，因此$p_{\lbrace h,l \rbrace}$表示在知道一个高信号和一个低信号之后的二阶后验。例如，对于实体$i$，我们可知对于任意不同的$j,k\neq i$，都有$p_{\lbrace h,l \rbrace}=\operatorname{Pr}\left(S_k=h \mid S_i=h, S_j=l\right)$。在这种情况下，实体$i$首先更新关于状态$T$的后验，即$Pr(T=t|S_i=s_i)$，这成为信念，以便进行第二轮的贝叶斯更新。

## The Bayesian Truth Serum
在这一节中，我们解释了Prelc（2004）的原始贝叶斯真理血清（BTS）。虽然我们介绍的是这个机制的二进制版本，但BTS是针对任意数量的信号而定义的。

在BTS中，每一个实体$i$都需要提供两个报告：
1. 信息报告：$x_i\in\lbrace 0,1 \rbrace$表示实体$i$报告的信号
2. 预测报告：$y_i\in[0,1]$表示实体$i$对人群中高信号出现的频率的预测

BTS中实体$i$的分数包括以下3步：
1. 对于任意实体$j\neq i$，计算除了$i$和$j$以外，所有实体报告信号的算术平均：
$$
\bar{x}_{-i j}=\frac{1}{n}\left(\left(\sum_{k \neq i, j} x_k\right)+1\right)
$$
2. 对于任意实体$j\neq i$，计算除了$i$和$j$以外，所有实体对高低两种信号频率的预测的几何平均：
$$
\bar{y}_{-i j}=\left(\prod_{k \neq i, j} y_k\right)^{\frac{1}{n-2}}, \quad \bar{y}_{-i j}^{\prime}=\left(\prod_{k \neq i, j}\left(1-y_k\right)\right)^{\frac{1}{n-2}}
$$
3. 计算实体$i$的BTS评分：
$$
\begin{aligned}
& u_i=\underbrace{\sum_{j \neq i}\left(x_i \ln \left(\frac{\bar{x}_{-i j}}{\bar{y}_{-i j}}\right)+\left(1-x_i\right) \ln \left(\frac{1-\bar{x}_{-i j}}{\bar{y}_{-i j}^{\prime}}\right)\right)}_{\text {information score }} \\
& +\underbrace{\sum_{j \neq i}\left(\bar{x}_{-i j} \ln \left(\frac{y_i}{\bar{x}_{-i j}}\right)+\left(1-\bar{x}_{-i j}\right) \ln \left(\frac{1-y_i}{1-\bar{x}_{-i j}}\right)\right)}_{\text {prediction score }}
\end{aligned}
$$
这里注释提到，BTS有两种，一种是无限数量的实体，一种是有限数量的实体，这篇论文关注的是后者。此外BTS使用了拉普拉斯平滑来避免出现为0的概率。

此外，这里和我之前理解的不那么一样，具体来说，之前我以为是直接计算所有人的算术平均和几何平均，但是从这个论文来看，它是分别计算了去掉每一个实体以后的平均值并求和。在人很多的时候，去掉一个人对均值也没影响，而人很少的时候，这个均值就会有波动了，进而导致激励相容不一定成立。

这对$n \rightarrow \infty$来说很简单，因为公式中对$j\neq i$的求和可以用只用一个随机选择的$j\neq i$计算的信息分和预测分来代替。

如果所有代理人（1）报告他们的真实信号和（2）预测人口中高信号的频率是他们的信号后验，则BTS机制是严格的贝叶斯-纳什激励相容。

**定理**1：当$n\rightarrow \infty$且所有先验可接受时，BTS是严格贝叶斯纳什激励相容的。

Prelec评论说，这个结果对于适当大的、有限的$n$也是成立的，实际的门槛取决于共同的先验。然而，BTS不需要为小群体的实体调整激励。此外，对于小群体来说，它不需要满足临时个体理性（interim IR），也就是说，实体的期望收益可以是负的。

**定理**2：当$n=3$时，BTS并不满足贝叶斯纳什激励相容或个体理性。

BTS的这种局限性可以从Prelec对BTS的处理中得到理解。一般来说，BTS兼容Bayes-Nash激励所需的代理数量取决于先验，而且很难确定。尽管如此，不同地方对BTS的讨论仍然没有注意到这个重要的注意事项，例如（Jurca和Faltings 2008；Chen和Pennock 2010）。出于这个原因，我们提供一个具体的例子。这个例子不是唯一的，也不依赖于$n=3$。

**例**1：（BTS且$n=3$）考虑三个代理人共享以下先验，$m=2$（两个状态）：$Pr(T=2)=0.7, Pr(S=h|T=2)=0.8, Pr(S=h|T=1)=0.1$。基于此，后验信号信念分别是$p_{\{h\}}=\operatorname{Pr}\left(S_j=h \mid S_i=h\right)=0.764$和$p_{\{l\}}=\operatorname{Pr}\left(S_j=h \mid S_i=l\right)=0.339$。

【个人理解】$P(T=2)=0.7,P(T=1)=0.3,P(S=h|T=2)=0.8,P(S=h|T=1)=0.1$
$$
\begin{aligned}
P(S_i=h)&=\sum_{t=1}^m P\left(S_i=h \mid T=t\right) P(T=t)\\
&=P(S_i=h|T=1)P(T=1)+P(S_i=h|T=2)P(T=2)\\
&=0.1*0.3+0.8*0.7\\
&=0.59
\end{aligned}
$$
$$
P(T=1|S_i=h)=\frac{P(S_i=h|T=1)P(T=1)}{P(S_i=h)}=\frac{0.1*0.3}{0.59}=0.0508
$$
$$
P(T=2|S_i=h)=\frac{P(S_i=h|T=2)P(T=2)}{P(S_i=h)}=\frac{0.8*0.7}{0.59}=0.9491
$$
$$
\begin{aligned}
P(S_j=h|S_i=h)&=\sum_{t=1}^m \operatorname{Pr}\left(S_j=h \mid T=t\right) \operatorname{Pr}\left(T=t \mid S_i=h\right) \\
&=P(S_j=h|T=1)P(T=1|S_i=h)+P(S_j=h|T=2)P(T=2|S_i=h)\\
&=0.1*0.0508+0.8*0.9491=0.764
\end{aligned}
$$
由此$p_{\{h\}}=\operatorname{Pr}\left(S_j=h \mid S_i=h\right)=0.764$，$p_{\{l\}}=\operatorname{Pr}\left(S_j=h \mid S_i=l\right)=0.339$也是类似的算法。

根据上面这个计算，可以认为，一个自己持有高信号的实体，认为其他人也持有高信号的概率是0.764；而一个自己持有低信号的实体，认为其他人持有高信号的概率是0.339。

考虑实体$i=1$，假设实体2和3都说真话。假设$S_1=h$，即实体1真实的报告是$x_1=1,y_1=0.764$。则当实体1说真话时，对应着实体$j=2$的评分公式计算的期望分数应该是：
$$
E\left[\ln \left(\frac{\bar{X}_{-12}}{\bar{Y}_{-12}}\right)+\bar{X}_{-12} \ln \left(\frac{0.764}{\bar{X}_{-12}}\right)+\left(1-\bar{X}_{-12}\right) \ln \left(\frac{1-0.764}{1-\bar{X}_{-12}}\right)\right]
$$
该期望值是相对随机变量$\bar{X}_{-12}$和$\bar{Y}_{-12}$而言的。当概率$p_{\{h\}}=0.764$时，实体1会认为$\bar{x}_{-12}=(1+1)/3=2/3$且$\bar{y}_{-12}=0.764$，同时概率$1-p_{\{h\}}=0.236$，可得$\bar{x}_{-12}=(0+1)/3=1/3$且$\bar{y}_{-12}=p_{\{l\}}=0.339$。给定上述内容，我们可以得到期望信息分是$0.764ln(\frac{2/3}{0.764})+0.236ln(\frac{1/3}{0.339})=-0.108$，而期望预测分是$0.764((2/3)ln(\frac{0.764}{2/3})+(1/3)ln(\frac{0.236}{1/3}))+0.236((1/3)ln(\frac{0.764}{1/3})+(2/3)ln(\frac{0.236}{2/3})=-0.117$，由此可得期望分是-0.225。而当公式中的$j=3$时，用同样的方法计算得到的实体1说真话对应的分数是-0.450，因此BTS不满足个体理性。

如果实体1谎报，且$x_1=0$，而仍然报告$y_1=0.764$，则期望信息分（$j=2$）会是$E[ln(\frac{1-\bar{X}_{-13}}{\bar{Y}'_{-13}})]=0.764ln(\frac{1/3}{0.236})+0.236ln(\frac{2/3}{0.661})=0.266$，预测分是0.149，类似的，考虑$j=3$可得总期望分是0.298，也就是说实体1谎报更好。

**例**2：（BTS且$n\rightarrow \infty$）考虑相同的先验但是数量更多的实体。在极限的情况下，关注实体1的信念，随机变量$\bar{X}_{-ij},\bar{Y}_{-ij},\bar{Y}'_{-ij}$以1的概率各自取值：
$$
\begin{aligned}
\bar{X}_{-1 j} & =\lim _{n \rightarrow \infty} \frac{1}{n}\left((n-2) p_{\{h\}}+1\right)=p_{\{h\}} \\
\bar{Y}_{-1 j} & =\lim _{n \rightarrow \infty}\left(\left(p_{\{h\}}^{(n-2) p_{\{h\}}}\right)\left(p_{\{l\}}^{(n-2)\left(1-p_{\{h\}}\right)}\right)\right)^{1 /(n-2)} \\
& =\left(p_{\{h\}}^{p_{\{h\}}}\right)\left(p_{\{l\}}^{1-p_{\{h\}}}\right)=0.631, \\
\bar{Y}_{-1 j}^{\prime} & =\left(1-p_{\{h\}}\right)^{p_{\{h\}}}\left(1-p_{\{l\}}\right)^{1-p_{\{h\}}}=0.301 .
\end{aligned}
$$
如果实体1如实报告（$x_1=1,y_1=0.764$），则它的期望信息分是$ln(\frac{0.764}{0.631})=0.191$，期望预测分是$0.764ln(0.764/0.764)+(1-0.764)ln(\frac{1-0.764}{1-0.764})=0$，即总分0.191。谎报$x_1=0$则会得到期望信息分为-0.243。在这个例子中，$n$足够大，取极限时BTS是贝叶斯纳什激励相容的。

在证明了BTS中取值较小的$n$的激励调整和临时IR的失败后，我们还对其数字上的稳健性做了如下观察：

**定理**3：对后验报告$y_i\in\lbrace0,1 \rbrace$而言BTS的分数是无界负数。

## Robust Bayesian Truth Serum
在本节中，我们介绍了稳健贝叶斯真理血清（RBTS）。RBTS对于每一个$n\geq 3$来说都是激励相容的，事后个体理性的（意味着对于任何结果，没有实体的收益是负的），并且在数字上是稳健的。我们首先介绍正确计分规则（不太确定这里的proper怎么翻译）。

**正确计分规则**是可以用于激励理性的实体如实宣称自己关于对未来事件的可能性的私人信念的函数。

**定义**2：（二值计分规则）给定可能的结果$\Omega=\{0,1\}$和关于结果$\omega=1$的概率的报告$y\in[0,1]$，一个二值计分规则$R(y,\omega)$根据报告$y$和发生的结果$\omega$分配一个分数。

首先，实体需要回答自己的信念报告$y\in[0,1]$。其次，事件$\omega\in\{0,1\}$发生了（由机制来观察）。最后，实体收到报酬$R(y,\omega)$。

**定义**3：（严格正确计分规则）如果一个二值计分规则能使得一个实体在如实报告自己信念$p\in[0,1]$时最大化自己的期望得分，则该规则是正确的。且如果一个实体仅在如实报告时才能最大化期望得分，则该规则是严格正确的。

一个严格正确计分规则的例子是二值二次函数计分规则$R_q$，经过归一化处理，给出的分数在0和1之间：
$$
\begin{aligned}
& R_q(y, \omega=1)=2 y-y^2 \\
& R_q(y, \omega=0)=1-y^2 .
\end{aligned}
$$
**定理**4：（例，Selten，1998）二值二次计分规则$R_q$是严格正确的。

请注意，如果人们对正确计分规则进行正负变换，该规则仍然是正确的。关于一般的适当得分规则的更详细讨论，我们参考Gneiting和Raftery（2007）的文章。

### The RBTS Mechanism
首先，每一个实体$i$都需要提供两个报告：
1. 信息报告
2. 预测报告

在第二步，针对每个实体$i$，选择一个参考实体$j=i+1$（模数$n$，这里没看懂）和一个对等实体$k=i+2$（模数$n$）并计算
$$
y_i^{\prime}=\left\{\begin{array}{lll}
y_j+\delta, & \text { if } & x_i=1 \\
y_j-\delta, & \text { if } & x_i=0
\end{array}\right.
$$
其中$\delta=min(y_j,1-y_j)$。实体$i$的RBTS分数为：
$$
u_i=\underbrace{R_q\left(y_i^{\prime}, x_k\right)}_{\text {information score }}+\underbrace{R_q\left(y_i, x_k\right)}_{\text {prediction score }}
$$
**例**3：（RBTS且$n=3$）我们使用和例1中一样的设定来解释RBTS，即$p_{\{h\}}=0.764,p_{\{l\}}=0.339$。此外，我们可以注意到$p_{\{h,h\}}=0.795$且$p_{\{l,h\}}=0.664$。考虑实体1的视角，令实体2和3扮演参考$j$和对等$k$的角色。假设实体2和3都说真话。首先解释$S_1=h,S_2=l,S_3=l$时的计算。如果实体1说真话，因为$y_2=0.339$且$\delta=0.339$，可得$y'=y_2+\delta=0.339+0.339=0.678$。因为$x_3=1$，实体1的信息分是$2y_1'-y_1'^2=2(0.678)-0.678^2=0.896$。因为$y_1=0.764$且$x_3=1$，预测分是$2(0.764)-0.764^2=0.944$。总分是1.84.

为了确定当$S_1=h$时实体1说真话是最优解，我们需要考虑期望得分，进而考虑实体2和3的信号的分布。针对预测报告，因为$R_q(y_1,x_3)$是严格正确的，所以一定是真实的。实体1的期望预测分是$0.764(2(0.764)-0.764^2)+0.236(2(0.236)-0.236^2)=0.820$。针对期望信息分，首先考虑如实报告$x_1=1$。在这种情况下，$y_1'$是由实体2的实际预测报告向上取整的，实体1的期望信息分是：
$$
\begin{aligned}
& \operatorname{Pr}\left(S_2=h \mid S_1=h\right) \\
& \quad\left[\quad \operatorname{Pr}\left(S_3=h \mid S_1=h, S_2=h\right) R_q(0.764+0.236,1)\right. \\
& \left.\quad+\operatorname{Pr}\left(S_3=l \mid S_1=h, S_2=h\right) R_q(0.764+0.236,0)\right] \\
& +\operatorname{Pr}\left(S_2=l \mid S_1=h\right) \\
& {\left[\quad \operatorname{Pr}\left(S_3=h \mid S_1=h, S_2=l\right) R_q(0.339+0.339,1)\right.} \\
& \left.\quad+\operatorname{Pr}\left(S_3=l \mid S_1=h, S_2=l\right) R_q(0.339+0.339,0)\right] \\
& =\quad p_{\{h\}}\left[p_{\{h, h\}}\left(2(1)-1^2\right)+\left(1-p_{\{h, h\}}\right)\left(1-1^2\right)\right] \\
& \quad+\left(1-p_{\{h\}}\right)\left[p_{\{h, l\}}\left(2(0.678)-0.678^2\right)\right. \\
& \left.+\left(1-p_{\{h, l\}}\right)\left(1-0.678^2\right)\right]=0.79 .
\end{aligned}
$$
针对报告$x_1=0$，期望信息分是：
$$
\begin{aligned}
& p_{\{h\}}\left[\quad p_{\{h, h\}} R_q(0.764-0.236,1)\right. \\
& \left.+\left(1-p_{\{h, h\}}\right) R_q(0.764-0.236,0)\right] \\
& +\left(1-p_{\{h\}}\right)\left[\quad p_{\{h, l\}} R_q(0.339-0.339,1)\right. \\
& \left.+\left(1-p_{\{h, l\}}\right) R_q(0.339-0.339,0)\right]=0.664 \\
&
\end{aligned}
$$
实体1说真话时期望信息分最高。

注意，对于任意$n\geq 3$，RBTS是严格贝叶斯纳什激励相容且可接受先验的。我们会在下一部分证明这一点。

## IC（激励相容）
在建立RBTS的激励相容性时，我们首先提出了一些技术性的定理。第一个定理还建立了随机相关性，因此，对于不同的信号观测，信号后验是不同的。然后，我们介绍了一个适当的评分规则，用于诱导信号而不是信念报告，并将其作为分析RBTS的基础部分。

**引理**5：对于所有可接受先验，$1>p_{\{h\}}>Pr(S_j=h)>p_{\{l\}}>0$都成立。

证明：可接受先验的完全混合属性确保先验总是内部的，且$1>p_{\{h\}}>0$以及$1>p_{\{l\}}>0$。此外，如果$p_{\{h\}}>Pr(S_j=h)$，则这意味着$Pr(S_j=h)>p_{\{l\}}$，因为
$$
\begin{aligned}
&\operatorname{Pr}\left(S_j=h\right)=  p_{\{h\}} \operatorname{Pr}\left(S_i=h\right) +\operatorname{Pr}\left(S_j=h \mid S_i=l\right) \operatorname{Pr}\left(S_i=l\right) \\
&\Leftrightarrow \quad  \operatorname{Pr}\left(S_j=h\right)\left(1-p_{\{h\}}\right)=p_{\{l\}}\left(1-\operatorname{Pr}\left(S_j=h\right)\right) \\
&\underbrace{\Rightarrow}_{p_{\{h\}}>\operatorname{Pr}\left(S_j=h\right)}  \operatorname{Pr}\left(S_j=h\right)>p_{\{l\}} .
\end{aligned}
$$
剩下的就是给定可接受先验的情况下证明$p_{\{h\}}>Pr(S_j=h)$。证明的剩余部分由3步组成：

首先，将每一个状态与$H$和$L$这两个组中的一个联系起来。

### A Proper Scoring Rule for Eliciting Signals: The “Shadowing” Method

## Other Properties and Discussion