---
title: 论文记录-Peer Prediction-Based Trustworthiness Evaluation and Trustworthy Service Rating in Social Networks
date: 2022-11-10 21:50:32
tags:
    - trustworthiness
    - peer prediction
categories: 论文
description: 对等预测，判断有没有说真话
---
# Peer Prediction-Based Trustworthiness Evaluation and Trustworthy Service Rating in Social Networks

## Abstract
随着基于社交网络的在线应用的发展，出现了许多不同的方法来评估这些应用所提供的服务。终端用户就消费者的体验或意见所做的报告通常被用来评价不同的在线服务的质量。因此，确保用户报告的真实性，以及检测恶意用户的不诚实报告，都成为实现此类服务评级准确性的重要问题。在本文中，我们提出并评估了一个基于私人先验的同伴预测的可信服务评级系统，该系统要求用户报告他们对其同伴是否会报告高质量的服务意见的先验和后验信念。这些报告被送到一个数据处理中心，该中心通过应用严格恰当的评分规则来评估用户的可信度，并删除从可信度等级低的用户那里收到的报告。这种同行预测方法与激励用户诚实报告的激励措施相兼容。此外，还提出了一个不可靠指数，以识别恶意用户，以及在对质量进行判断时有高错误率的故障或不可靠的用户。因此，具有高不可靠值的报告也将被排除在服务评级系统之外。通过结合可信度和不可靠度，恶意用户面临的困境是，当他们的报告是虚假的，他们不能同时获得高可信度和低不可靠度的评级。仿真结果表明，所提出的基于同伴预测的可信服务评级能够有效地识别恶意行为和不可靠行为，并激励用户如实报告，而且所提出的系统能够达到比较高的服务评级准确率。

## Intro
研究背景：
1. 互联网服务发展很好
2. 对互联网服务进行质量评级是一种常用的帮助用户进行选择的方法
3. 现阶段质量评级的公平性和实用性受恶意用户的影响很大，有必要进行信任评估并激励用户输出真实反馈

本文：基于对等预测的社会网络可信服务评级系统
假设：服务质量是一个独立于用户主观判断的客观评价

### 相关研究
略

### 贡献与组织
贡献：
1. 我们在社交网络的服务评级系统中引入了私有先验对等预测。通过某些严格恰当的打分规则获得的用户信任度被制定出来，以激励用户如实地报告。我们分析了基本对等预测机制在判断和报告的误报和漏报概率方面的激励兼容性。
2. 我们提出了一个不可靠指数来消除服务评级系统中不可靠的报告。通过应用不可靠指数，恶意用户面临着一个困境，即他们在提供虚假报告时不能同时获得高可信度和低不可靠度。然而，诚实的用户的最佳选择仍然是如实报告，即使是功能差的，判断错误率高的。
3. 基于提出的用户可信度和不可靠指数，我们设计了一个服务评级框架。在这个框架中，信任度被用来评估主体用户的报告是否不诚实，用户是否是恶意的。另一方面，不可靠指数被引入以确定报告是否可靠，但不考虑用户的类型，即诚实或恶意。通过从最终的评级程序中去除不可靠度高的反馈报告和从可信度低的用户那里收到的报告，可以实现准确和可信的服务评级。

## 基于用户报告融合的服务评级的数学模型
重复了intro中的内容，表示本文设计了这样那样的机制

### 系统模型
场景：$N$个用户分布在社交网络中，共用一个服务平台。服务质量$Q$是一个二元评价，低质量和高质量分别用$\lbrace l,h\rbrace$表示，且服务质量是一个客观事实，即所有诚实的用户会给出相同的评价，而不受他们个人主观标准的影响。如图1所示，每个用户$i$收到服务$m$，然后决定他们对$m$的质量评价记作$S_i=s_i\in\lbrace l,h \rbrace$。用户同样会把服务评级上报到云，这些数据会有数据处理中心（DPC）处理。例如，意见报告$x_i\in \lbrace 0,1 \rbrace$是通过应用报告策略$r_i:S_i\rightarrow \lbrace 0,1 \rbrace$生成的。如果用户是诚实的，则当$S_i=h$时，用户$i$会向云报告$x_i=1$，当$S_i=l$时，用户会报告$x_i=0$。我们假设$x_i$是云发布到社会服务评估平台半公开信息，并且可以被与用户$i$有着好友关系的DPC和其他用户通过社交网络观察到。此外，$S_i$是只有用户$i$知道的私有或本地信息，其他用户甚至云都无法得到它。

![图1](https://github.com/likun1208/image/blob/master/PeerPrediction-1.png?raw=true)

### 基于用户报告融合的服务评级 
判断的错误：实际是高质量的服务被错误的判定为低质量

用户$i$判断错误的概率：$P_{fa,i}=P(S_i=l|Q=h)$，简化记作$P_{fa}$.

判断的漏检：实际是低质量的服务被认为是高质量

判断漏检的概率：$P_{md,i}=P(S_i=h|Q=l)$，简化记作$P_{md}$

如前所述，质量是一个客观事实，因此诚实和恶意用户对它的判断都是相似的。因此我们假设判断错误和漏检的概率都小于0.5（就是说判断与实际不符的概率比随便猜一个要低）。

报告的错误：实际是高质量的服务被错误的报告为低质量（也许是判断错了，也许是故意的）

报告错误的概率：$P_{f,i}=P(x_i=0|Q=h)$，简化记作$P_f$

报告的漏检：实际是低质量的服务被错误地报告为高质量（同样，也许是判断错了，也许是故意的）

报告漏检的概率：$P_{m,i}=P(x_i=1|Q=l)$，简化记作$P_m$

用户类型$\theta_i \in \lbrace 0,1 \rbrace$，0表示诚实，1表示恶意，恶意用户的判断是高但故意报告低的概率为$P_{f,i}^c\in[0,1]$，判断是低但故意报告高的概率为$P_{m,i}^c\in [0,1]$

假设：无论判断准确率如何，诚实用户永远如实报告

对于任意用户$i$，可得：
$$
\begin{aligned}
P_{f, i} &=\left\{\begin{array}{ll}
\left(1-P_{f a, i}\right) P_{f, i}^{c}+P_{f a, i}\left(1-P_{f, i}^{c}\right), & \theta_{i}=1 ; \\
P_{f a, i}, & \theta_{i}=0 ;
\end{array}\right.\\
P_{m, i} &=\left\{\begin{array}{ll}
\left(1-P_{m d, i}\right) P_{m, i}^{c}+P_{m d, i}\left(1-P_{m, i}^{c}\right), & \theta_{i}=1 ; \\
P_{m d, i}, & \theta_{i}=0 .
\end{array}\right.
\end{aligned}
$$

这里简单描述一下就是说，诚实用户的报告与实际不符的概率和他判断出错的概率一致，而恶意用户的报告与实际不符的概率则分为两部分：判断没出错但上报的时候故意骗人+判断出错了但上报的时候没骗人。

根据云收到的用户报告，DPC可以获得每个用户的可信度$T_i$，并通过应用以下规则做出服务评级的决定：
$$
R=\sum_{i \in T} x_{i}\left\{\begin{array}{ll}
<n, & \text { the DPC rates the service } Q=l \\
\geq n, & \text { the DPC rates the service } Q=h
\end{array}\right.
$$
其中$n$是服务评级阈值。说人话就是大家总分达标就把服务判定为高质量，否则是低质量。公式中的$T$表示高可信的诚实用户集合。

## 对等预测
这一部分具体介绍私有先验对等预测机制，以及可信度的计算。
### 私有先验对等预测机制
私有先验对等预测是一种激励相容的机制，最初是为了激励代理人报告他们关于电子商务的私人先验和后验信号信念。在基础的私有先验对等预测机制中，每个代理$i$都和他的同行代理$j=i+1$成为一组，并被要求分别报告自己在观察信号前后的针对状态的先验和后验信号信念。根据两份报告，代理$i$的分数可以根据严格正确计分规则来计算。

1. 发给云的先验信念报告：在本研究所建立的系统中，任意两个收到相同服务的用户可以被看做是一对对等用户，由此建立了一种用户之间的关系和拓扑结构。为了对服务$m$的质量进行评级，我们认为每个用户$i$都有从其他收到相同服务$m$并将继续使用该服务的用户中随机选择的对等用户$j$。在体验服务之前，用户$i$需要向云上报他对于同事$j$会上报高质量信号即$x_j=1$的先验信念$y_{ij}\in[0,1]$，这个上报也可以称之为信息报告。则$y_{ij}$的计算方式：
$$
\begin{aligned}
y_{i j}=& P_{i}\left(x_{j}=1\right) \\
=& P_{i}\left(x_{j}=1 \mid Q=h\right) P_{i}(Q=h) \\
&+P_{i}\left(x_{j}=1 \mid Q=l\right) P_{i}(Q=l) \\
\triangleq & P\left(x_{j}=1 \mid S_{i}=h\right) P\left(S_{i}=h\right) \\
&+P\left(x_{j}=1 \mid S_{i}=l\right) P\left(S_{i}=l\right)
\end{aligned}
$$
其中，$P_{i}\left(x_{j}=1 \mid Q=h\right)$和$P_{i}\left(x_{j}=1 \mid Q=l\right)$可以从用户$j$在网络中之前发布过的报告$x_j$中获取。前者表示当用户$i$判断服务质量为高质量（即$S_i=h$）时用户$j$提交的报告也说服务是高质量。用户$i$的判断是私有信息，只有他自己知道，而先验信念$P_i(Q=h)$是用户$i$对服务质量的主观先验，与$P(S_i=h)$相同。类似地，$P_i(Q=l)$也等于$P(S_i=l)$。因此，我们可以得到上面公式中用$\triangleq$相连的等式。

2. 发给云的后验信念报告：体验过服务后，用户$i$决定他自己对服务质量的判断$S_i=s_i$，然后将他关于对等用户$j$会上报该服务的质量评估为高质量这件事的后验信念（或称之为预测报告）发送给云，记作$y_{ij}^{'}(s_i)\in[0,1]$。则$y_{ij}^{'}$可以表示如下：
$$
\begin{aligned}
y_{i j}^{\prime}\left(s_{i}\right)=& P_{i}\left(x_{j}=1 \mid S_{i}=s_{i}\right) \\
=& P\left(x_{j}=1 \mid Q=h\right) P\left(Q=h \mid S_{i}=s_{i}\right) \\
&+P\left(x_{j}=1 \mid Q=l\right) P\left(Q=l \mid S_{i}=s_{i}\right) .
\end{aligned}
$$
与前文分析类似，$y_{ij}^{'}$可以被分为以下两种情况：
$$
\begin{aligned}
y_{i j}^{\prime}(l) &=\frac{\varphi_{1}\left(1-P_{f, j}\right)+\varphi_{2} P_{m, j}}{\varphi_{1}+\varphi_{2}} \\
y_{i j}^{\prime}(h) &=\frac{\varphi_{3}\left(1-P_{f, j}\right)+\varphi_{4} P_{m, j}}{\varphi_{3}+\varphi_{4}}
\end{aligned}
$$
其中，
$$
\begin{array}{l}
\varphi_{1}=P_{f a, i} P(Q=h), \quad \varphi_{2}=\left(1-P_{m d, i}\right) P(Q=l), \\
\varphi_{3}=\left(1-P_{f a, i}\right) P(Q=h), \quad \varphi_{4}=P_{m d, i} P(Q=l)
\end{array}
$$
$\varphi_1$是服务为高质量且用户$i$出现误报的概率，$\varphi_2$是服务为低质量且用户$i$没有漏检的概率，加起来就是用户$i$给出的判断是低质量的概率。

类似地，$\varphi_3$是服务为高质量且用户$i$没有误报的概率，$\varphi_4$是服务为低质量且用户$i$漏检的概率，加起来就是用户$i$给出的判断是高质量的概率。

$$
\begin{align}
y_{ij}^{'}(l)&= P\left(x_{j}=1 \mid Q=h\right) P\left(Q=h \mid S_{i}=l\right)+P\left(x_{j}=1 \mid Q=l\right) P\left(Q=l \mid S_{i}=l\right)\\
&=(1-P_{f,j})P(Q=h|S_i=l)+P_{m,j}P(Q=l|S_i=l)\\
&=P(S_i=l|Q=h)P(Q=h)(1-P_{f,j})/P(S_i=l)+P(S_i=l|Q=l)P(Q=l)P_{m,j}/P(S_i=l)\\
&=P_{fa,i}P(Q=h)(1-P_{f,j})/P(S_i=l)+(1-P_{md,i})P(Q=l)P_{m,j}/P(S_i=l)\\
&=\varphi_1(1-P_{f,j})/(\varphi_1+\varphi_2)+\varphi_2P_{m,j}/(\varphi_1+\varphi_2)\\
&=\frac{\varphi_1(1-P_{f,j})+\varphi_2P_{m,j}}{\varphi_1+\varphi_2}
\end{align}
$$

$y_{ij}^{'}(h)$也是类似的计算方法，就不写了。

如前文定义，$x_{ij}$是用户$i$在体验服务之前对$x_j=1$的先验判断。在用户$i$体验服务并发送报告$s_i=h$后，他理所当然有很大概率会判断$x_j=1$，即$y_{ij}^{'}(h)>y_{ij}$，这意味着用户$i$对$x_j=1$的先验信念会被提高。与之相反，如果用户$i$收到了低质量服务，则$y_{ij}>y_{ij}^{'}(l)$。但是，当存在会提供不真实的服务评价的恶意用户时，上述不等式的关系将不再成立。引理1证明了会使得$y_{i j}^{\prime}(h)>y_{i j}>y_{i j}^{\prime}(l)$出现的充分条件。

引理1：在私有先验预测机制中，对于每个用户$i$，他对用户$j$的先验和后验信念报告分别是$y_{ij}$和$y^{'}_{ij}$，当所有用户都满足$P_{fa}+P_{md}<1$且$P_f+P_m<1$时，$y_{i j}^{\prime}(h)>y_{i j}>y_{i j}^{\prime}(l)$成立。

证明：在附录，先不看了

备注：假设$P_{fa}<0.5$且$P_{md}<0.5$，对于所有用户而言，$P_{fa}+P_{md}<1$都成立。根据公式1和2，对于诚实用户即$\theta_i=0$，我们可得$P_{f,i}+P_{m,i}<1$.另一方面，对于不诚实用户（$\theta_i=1$），$P_{f,i}+P_{m,i}<1$能否成立取决于他的两个谎报概率$P_{f,i}^c$和$P_{m,i}^c$。有着相对高的$P_f^c>0.5$且/或$P_m^c$的彻底的恶意用户会同样有高$P_f>0.5$且/或$P_m>0.5$。有上述两种或两种之一作弊行为的用户可以根据他们以前的报告很容易被识别出来，因为其中的错误报告概率很高。如果评分系统移除有着较高的$P_f$和/或$P_m$的用户报告，则这些恶意报告就不会在系统更新服务评价时造成影响。因此，要实现连续的欺骗，恶意用户需要控制他们的$P_f^c$和$P_m^c$，从而伪装成偶尔可信的用户，以确保$P_f<0.5$且$P_m<0.5$。因此，引理1中的条件$P_f+P_m<1$是合理的，且这种情况下，不等式$y_{i j}^{\prime}(h)>y_{i j}>y_{i j}^{\prime}(l)$始终成立。

3. 推断意见报告：用户$i$发送他对同事$j$会报告$x_j=1$这件事的先验和后验信念概率，而不是报告自己对服务质量的私有评价$S_i$或$x_i$。我们可以注意到，$x_i$和$x_j$都不由相关用户直接提供。在基础私有先验对等预测中，用户$i$仅向云发送报告$y_{ij}$和$y_{ij}^{'}(s_i)$，基于此，DPC推断意见报告$x_i$并将它向社交服务评估平台公布。推断意见报告$x_i$的计算方式如下：
$$
x_{i}=x\left(y_{i j}, y_{i j}^{\prime}\right)=\left\{\begin{array}{ll}
1, & y_{i j}^{\prime}>y_{i j} \\
0, & y_{i j}^{\prime}<y_{i j}
\end{array}\right.
$$
备注：根据引理1，当用户$i$和用户$j$都满足$P_{fa}+P_{md}<1$且$P_f+P_m<1$时，$y_{ij}^{'}(h)>y_{ij}>y_{ij}^{'}(l)$。换言之，当用户$i$在体验服务后对它的质量判断为高质量（$S_i=h$），不等式$y_{ij}^{'}(h)$始终成立。由此，根据公式8，DPC推断意见报告为$x_i=1$因为$y_{ij}^{'}>y_{ij}$。因此这个推断报告$x_i=1$与用户$i$的真实判断$S_i=h$是一致的。对称地，当$S_i=l$时，公式8仍然可以推断真实意见报告$x_i=0$。因此，在$P_{fa}+P_{md}<1$和$P_f+P_m<1$的条件下，由公式8所决定的规则可以如实反映诚实用户的判断。

4. 用户可信度：基于报告$y_{ij}$和$y_{ij}^{'}$，DPC通过一个确定的计分规则来计算用户$i$的可信度，信任度低的用户被归类为恶意用户，他们的报告将在服务评级系统中不被考虑。接下来，我们首先介绍严格正确的计分规则，它可以激励用户提供真实的报告$y_{ij}$和$y_{ij}^{'}$。

定义1（严格正确的计分规则）：如果一个二进制评分规则导致代理人可以通过如实提供自己的报告$y\in[0,1]$来最大化自己的得分，则该规则就是正确的，且如果一个代理人当且仅当如实报告时能最大化得分，则该规则是严格正确的。

分别如(9)和(10)所示的二元对数和二次函数评分规则是严格正确的，这在[30]中已经得到证明：

对数：
$$
\begin{array}{l}
R_{l}(y, \omega=1)=\ln y \\
R_{l}(y, \omega=0)=\ln (1-y) .
\end{array}
$$

二次函数：
$$
\begin{array}{l}
R_{q}(y, \omega=1)=2 y-y^{2} \\
R_{q}(y, \omega=0)=1-y^{2}
\end{array}
$$
在上面两组公式中，$\omega\in\lbrace 0,1\rbrace$表示一个二值报告。

我们定义用户$i$的可信度为关于$y_{ij},y_{ij}^{'},x_j$的函数：
$$
T_{i}=\alpha R\left(y_{i j}, x_{j}\right)+(1-\alpha) R\left(y_{i j}^{\prime}, x_{j}\right)+\beta
$$
 其中，$R(y,\omega)$是严格正确评分规则，$\alpha\in[0,1]$是先验和后验重要性的权重参数。此外，可信度在服务和评分过程中会进行累积。负面的信任度可以是金钱上的惩罚，也可以是对相应用户提供报告的限制，而负面的收益将作为正面的收益转移给用户，作为对他们荣誉和准确报告的奖励。因此，为了保证预算均衡，$\beta$如下计算：
 $$
 \beta=-\frac{1}{N} \sum_{k=1}^{N}\left[\alpha R\left(y_{k j}, x_{j}\right)+(1-\alpha) R\left(y_{k j}^{\prime}, x_{j}\right)\right]
 $$
在公式11（$T_i$的公式）中，$y_{ij}$和$y_{ij}^{'}$分别是用户$i$判定$S_i=s_i$前后的报告，$x_j$是DPC根据用户$j$的报告推断出的用户$j$的隐性意见报告。此外，根据上述分析，我们可以注意到，用户$i$的可信度是由用户$j$的推断意见报告$x_j$、用户$i$的先验信念报告$y_{ij}$和后验信念报告$y_{ij}^{'}$决定的。换句话说，一个用户的可信度与系统中其他用户的报告或推断的报告无关。因此，恶意用户的合作作弊对用户可信度的评价影响不大，这是由（11）定义的。

### 激励相容
如[33]所说，用户$i$提出的先验信念报告$y_{ij}$和后验信念报告$y_{ij}^{'}(s_i)$在时间上是分离的，因为它们分别发生在做出判断$S_i=s_i$前后。因此，$y_{ij}$和$y_{ij}^{'}(s_i)$是独立的，可得：
$$
\begin{aligned}
E\left[T_{i}\right]=& E\left[\alpha R\left(y_{i j}, x_{j}\right)\right]+E\left[(1-\alpha) R\left(y_{i j}^{\prime}, x_{j}\right)\right]+E[\beta] \\
=& \alpha\left(1-\frac{1}{N}\right) E\left[R\left(y_{i j}, x_{j}\right)\right] \\
&+(1-\alpha)\left(1-\frac{1}{N}\right) E\left[R\left(y_{i j}^{\prime}, x_{j}\right) \mid S_{i}=s_{i}\right] \\
&-\frac{1}{N} \sum_{k=1, k \neq i}^{N}\left[\alpha R\left(y_{k j}, x_{j}\right)+(1-\alpha) R\left(y_{k j}^{\prime}, x_{j}\right)\right],
\end{aligned}
$$
其中，$\alpha\left(1-\frac{1}{N}\right) R\left(y_{i j}, x_{j}\right)$和$(1-\alpha)\left(1-\frac{1}{N}\right) R\left(y_{i j}^{\prime}, x_{j}\right)$仍然是严格正确的[32]。

1. 二值对数评分规则
令$p_1=P(x_j=1)$且$p_2=P(x_j=1|S_i=s_i)$，由此可得：
$$
\begin{aligned}
E\left[T_{i}\right]=& \alpha\left(1-\frac{1}{N}\right)\left[p_{1} \ln y_{i j}+\left(1-p_{1}\right) \ln \left(1-y_{i j}\right)\right] \\
=& \alpha\left(1-\frac{1}{N}\right)\left[p_{1} \ln y_{i j}^{\prime}+\left(1-p_{1}\right) \ln \left(1-y_{i j}^{\prime}\right)\right] \\
&-\frac{1}{N} \sum_{k=1, k \neq i}^{N}\left[\alpha R\left(y_{k j}, x_{j}\right)+(1-\alpha) R\left(y_{k j}^{\prime}, x_{j}\right)\right] .
\end{aligned}
$$
分别求偏导：
$$
\begin{array}{l}
\frac{\partial E\left[T_{i}\right]}{\partial y_{i j}}=\alpha\left(1-\frac{1}{N}\right) \frac{p_{1}-y_{i j}}{y_{i j}\left(1-y_{i j}\right)}=0 \\
\frac{\partial E\left[T_{i}\right]}{\partial y_{i j}^{\prime}}=\alpha\left(1-\frac{1}{N}\right) \frac{p_{1}-y_{i j}^{\prime}}{y_{i j}^{\prime}\left(1-y_{i j}^{\prime}\right)}=0 .
\end{array}
$$
因此可得最优值：
$$
\begin{array}{l}
\hat{y}_{i j}=p_{1}=P\left(x_{j}=1\right) \\
\hat{y}_{i j}^{\prime}=p_{2}=P\left(x_{j}=1 \mid S_{i}=s_{i}\right)
\end{array}
$$
然后求二阶偏导，令$y_{ij}=\hat{y}_{ij}$且$y_{ij}^{'}=\hat{y}_{ij}^{'}$，可得：
$$
\begin{array}{l}
\left.\frac{\partial E^{2}\left[T_{i}\right]}{\partial y_{i j}^{2}}\right|_{y_{i j}=\hat{y}_{i j}}=\alpha\left(1-\frac{1}{N}\right) \frac{y_{i j}\left(y_{i j}-1\right)}{y_{i j}^{2}\left(1-y_{i j}\right)^{2}}<0, \\
\left.\frac{\partial E^{2}\left[T_{i}\right]}{\partial y_{i j}^{\prime 2}}\right|_{y_{i j}^{\prime}=\hat{y}_{i j}^{\prime}}=\alpha\left(1-\frac{1}{N}\right) \frac{y_{i j}^{\prime}\left(y_{i j}^{\prime}-1\right)}{y_{i j}^{\prime 2}\left(1-y_{i j}^{\prime}\right)^{2}}<0 .
\end{array}
$$
因此，当$y_{ij}=p_1$且$y_{ij}^{'}=p_2$时，$E[T_i]$可达到最大值，这意味着当且仅当用户$i$如实报告$y_{ij}$和$y_{ij}^{'}$时他可以获取最大的可信度。

2. 二值二次函数评分规则：
$$
\begin{aligned}
E\left[T_{i}\right] \\
=& \alpha\left(1-\frac{1}{N}\right)\left[p_{1}\left(2 y_{i j}-y_{i j}^{2}\right)+\left(1-p_{1}\right)\left(1-y_{i j}^{2}\right)\right] \\
&+(1-\alpha)\left(1-\frac{1}{N}\right)\left[p_{2}\left(2 y_{i j}^{\prime}-y_{i j}^{\prime}{ }^{2}\right)+\left(1-p_{2}\right)\left(1-y_{i j}^{\prime}{ }^{2}\right)\right] \\
&-\frac{1}{N} \sum_{k=1, k \neq i}^{N}\left[\alpha R\left(y_{k j}, x_{j}\right)+(1-\alpha) R\left(y_{k j}^{\prime}, x_{j}\right)\right] .
\end{aligned}
$$
仍然是求偏导，可以得到和对数函数一样的最优值。然后求二阶偏导，可得下述不等式恒成立：
$$
\frac{\partial E^{2}\left[T_{i}\right]}{\partial y_{i j}^{2}}=\frac{\partial E^{2}\left[T_{i}\right]}{\partial y_{i j}^{\prime 2}}=-2 \alpha\left(1-\frac{1}{N}\right)<0
$$

备注：无论是二值对数还是二值二次函数评分规则，$\partial^{2} E\left[T_{i}\right] / \partial y_{i j}^{2}<0$和$\partial^{2} E\left[T_{i}\right] / \partial {y^{'}_{i j}}^{2}<0$都成立，$E[T_i]$会在两个一阶导得到的最优值满足时取最大值。换言之，用户$i$当且仅当如实报告$y_{ij}$和$y_{ij}^{'}$时他可以获取最大的可信度。假设存在共谋，这意味着恶意用户可以相互联系并控制恶意行为。根据定义1，当对等用户$j$是恶意用户时，用户$i$谎报时得到的分数会比如实报告的分数低。例如，用户$i$体验了一个高质量任务，这意味着他的诚实报告满足$y_{ij}^{'}>y_{ij}$。但是，由于用户$j$是恶意的，他会谎报意见，即$x_j=0$，根据二值对数和二次函数评分规则，与如实报告相比，给出一个更低的$y_{ij}^{'}<y_{ij}$会使得用户$i$得到更高的评分。为了确保即使在社会网络中发生合作欺骗行为时，诚实的用户仍占主导地位，我们假设恶意用户的数量少于总数的一半。基于这个假设，拥有准确信息报告和预测报告的用户将长期获得更高的信任度；同时，恶意用户每次公布不诚实的报告导致作弊的意见报告时，都会受到信任度下降的惩罚。

感觉这篇文章和贝叶斯实话血清那个相似的地方就在于这里的评分函数形式。

## 基于用户可信度和不可靠度的服务评级
### 用户报告的不可靠度
在私有先验对等预测中，所有用户在体验服务之前都被要求报告他们关于对等用户会报告高质量的先验信念$y_{ij}=P_i(x_j=1)$。DPC可以从过去的报告$x_j$中推断出该报告，并由云发布，这意味着过去的报告$x_j$可以被$i$在社交网络中的其他好友、云和DPC获取。因此，对于恶意用户来说伪造信息报告$y_{ij}$是困难的。为了实现欺骗，恶意用户$i$需要控制他的信息并根据前文的公式预测报告，即当服务质量为低（$Q=l$）时有概率$p_{m,i}^{c}$会出现$y^{'}_{ij}=y_{ij}+\varepsilon(\varepsilon>0)$，当服务质量为高（$Q=h$）时有概率$P_{f,i}^{c}$出现$y^{'}_{ij}=y_{ij}-\varepsilon$。同时，恶意用户需要让$\varepsilon$尽可能小，从而避免当他们的对等用户是诚实用户时自己要因为大量损失评分和可信度而受到惩罚。此外，我们可以注意到误报和漏检并不一定来自于诚实用户的错误判断，同样也来自不诚实用户的欺骗行为。上述两种情况都被认为是不可靠的行为，需要在最终的服务评级中加以识别和删除。因此，有必要设置阈值来限制$y_{ij}$和$y_{ij}^{'}$之间的最小值。

接下来，我们分析了误报判断和漏检判断对分数的影响。分别对前文公式中的$P_{fa,i}$和$P_{md,i}$求导，可得：
$$
\begin{array}{l}
\frac{\partial y_{i j}^{\prime}(l)}{\partial P_{f a, i}}=\Phi_{1}\left(1-P_{m d, i}\right)\left(1-P_{f, j}-P_{m, j}\right) \\
\frac{\partial y_{i j}^{\prime}(l)}{\partial P_{m d, i}}=\Phi_{1} P_{f a, i}\left(1-P_{f, j}-P_{m, j}\right) \\
\frac{\partial y_{i j}^{\prime}(h)}{\partial P_{f a, i}}=-\Phi_{2} P_{m d, i}\left(1-P_{f, j}-P_{m, j}\right) \\
\frac{\partial y_{i j}^{\prime}(h)}{\partial P_{m d, i}}=-\Phi_{2}\left(1-P_{f a, i}\right)\left(1-P_{f, j}-P_{m, j}\right)
\end{array}
$$
其中，$\Phi_1=P(Q=h)P(Q=l)/(\phi_1+\phi_2)^2,\Phi_2=P(Q=h)P(Q=l)/(\phi_3+\phi_4)^2$。基于前文假设$P_{fa,i}<0.5,P_{md,i}<0.5,P_{f,j}+P_{m,j}<1$，可得：
$$
\frac{\partial y_{i j}^{\prime}(l)}{\partial P_{f a, i}}>\frac{\partial y_{i j}^{\prime}(l)}{\partial P_{m d, i}}>0, \quad \frac{\partial y_{i j}^{\prime}(h)}{\partial P_{m d, i}}<\frac{\partial y_{i j}^{\prime}(h)}{\partial P_{f a, i}}<0
$$
因此，在$Q=h$和$Q=l$这两种情况下，当用户$j$如实报告时，用户$i$的分数会随着$P_{fa,i}$和$P_{md,i}$的增加而减少。换言之，对于固定的$P_f,P_j,P(Q=h)$，判断准确率高的诚实用户将获得更高的分数和可信度，相比之下，那些判断错误率高的诚实用户和恶意用户则反过来保守地报告他们的预测，给出错误的报告，将分数的损失降到最低。在服务评级系统中，既不应该考虑恶意用户的隐性意见报告，也不应该考虑判断准确性低的诚实用户。为了识别出这两种不可靠的行为，我们通过用户$i$的先验信念报告$y_{ij}$和后验信念报告$y_{ij}^{'}$定义了不可靠指数来表征用户$i$的不可靠度：
$$
\rho_{i}=\left\{\begin{array}{ll}
\frac{\left|y_{i j}^{\prime}-P_{m, j}\right| P(Q=l)}{\left|y_{i j}^{\prime}-\left(1-P_{f, j}\right)\right| P(Q=h)} & y_{i j}^{\prime}<y_{i j} \\
\frac{\left|y_{i j}^{\prime}-\left(1-P_{f, j}\right)\right| P(Q=h)}{\left|y_{i j}^{\prime}-P_{m, j}\right| P(Q=l)}, & y_{i j}^{\prime}>y_{i j}
\end{array}\right.
$$
备注：在上面的公式中，第一种情况$y_{ij}^{'}<y_{ij}$表示，服务为低质量时$y_{ij}^{'}$越接近$P\lbrace x_j=1|Q=l \rbrace$，或者服务为高质量时$y_{ij}^{'}$越远离$P\lbrace x_j=1|Q=h \rbrace$，则$y_{ij}^{'}$越可靠。同时，对于$y_{ij}^{'}>y_{ij}$，当报告$y_{ij}^{'}$接近$P\lbrace x_j=1|Q=h \rbrace$且远离$P\lbrace x_j=1|Q=l \rbrace$，该报告可以被认为是可靠的。此外，根据该公式，$y_{ij}^{'}(l)$会随$P_{fa,i}$和$P_{md,i}$的增长而增加，且对$P_{fa,i}$比$P_{md,i}$更敏感。$y_{ij}^{'}(h)$随$P_{fa,i}$和$P_{md,i}$的增长而减少，且对$P_{md,i}$比$P_{fa,i}$更敏感。假设$P_{fa,i},P_{md,i}\in [0,1]$，我们可得$P_{m, j}<y_{i j}^{\prime}(l), y_{i j}^{\prime}(h)<1-P_{f, j}$，因此上面公式中关于不可靠度的定义可以改写为：
$$
\rho_{i}=\left\{\begin{array}{cl}
\frac{\left[y_{i j}^{\prime}-P_{m, j}\right] P(Q=l)}{\left[\left(1-P_{f, j}\right)-y_{i j}^{\prime}\right] P(Q=h)} & y_{i j}^{\prime}<y_{i j} \\
\frac{\left[\left(1-P_{f, j}\right)-y_{i j}^{\prime}\right] P(Q=h)}{\left[y_{i j}^{\prime}-P_{m, j}\right] P(Q=l)}, & y_{i j}^{\prime}>y_{i j}
\end{array}\right.
$$
为了计算用户报告的不可靠度，DPC需要基于历史数据观察每个用户的报告错误率$P_f,P_m$。此外，我们假设服务质量（记作$P(Q=l),P(Q=h)$）也可以根据较长的时间尺度和相对稳定的服务历史评级结果来获得。考虑到目前大多数基于服务的应用系统有能力提供这样的信息，这样的假设是可行和合理的。通过优化上述公式，不可靠度高的用户被认为是不确定的，他们可能是错误判断率高的诚实用户或恶意用户。来自这些用户的报告对于DPC评价服务质量是不可靠的。因此，DPC需要设置一个不可靠度的阈值，不可靠度超过该阈值的用户的报告将从服务评级程序中删除。阈值可以通过具有相对较高判断精度的诚实用户的典型错误率来设计。

接下来，我们描述前文公式中定义的用户不可靠度的有效性。以$Q=h$为例，恶意用户$i$为了实现欺骗会给出预测报告$y_{ij}^{'}=y_{ij}-\varepsilon<y_{ij}$。为了得到比阈值更低的不可靠度并使得自身的欺骗行为能对服务评级造成影响，用户$i$需要伪造报告$y_{ij}^{'}$使得它接近$P_{m,j}$并远离$1-P_{f,j}$。当$P_{f,i}<0.5$且$P_{m,i}<0.5$时，$y_{ij}^{'}$越小，不可靠度越低。另一方面，当$Q=h$时大多数诚实用户倾向于报告$x_j=1$。根据很前面的公式，当用户$i$的对等用户$j$给出的报告准确而诚实，则用户$i$的评分随$y_{ij}^{'}$的减小而减小。对称地，$Q=l$时也存在相同的困境。因此，恶意用户如果欺骗性地报告，很难同时获得高可信度和低不可靠度。然而，对于那些判断错误率相对较高的“功能不好”的诚实用户来说，最好的选择仍然是如实报告$y_{ij}$和$y_{ij}^{'}$。他们没有必要修改他们的$y_{ij}^{'}$，因为他们的利益是由信息和预测报告决定的分数和可信度，这个利益与他们的报告是否被DPC接受没有关系。

### 基于同行预测的服务评级
根据以上对用户可信度和不可靠度的分析，我们设计了基于私人优先同伴预测的服务评级方法，具体步骤如下。
1. 对于每个接受服务的用户$i$，在他/她的朋友中随机选择另一个不重叠的用户$j$作为$i$的同伴。
2. 要求用户$i$提供他/她的先验信念报告$y_{ij}\in[0,1]$，即他/她的同行$j$将向云报告高质量的概率。
3. 用户$i$体验服务，并作出自己对服务质量的判断$S_i=s_i$
4. 问用户的后验信念报告$y_{ij}^{'}\in[0,1]$
5. DPC计算不可靠度并移除超过阈值的用户报告
6. DPC从报告中推断用户的隐含意见报告$x_i$，移除低可信度的用户
7. DPC根据高可信度且低不可靠度的用户报告决定服务质量

## 仿真实验
实验部分略

## 总结
1. 基于云的服务评级架构
2. 基于私有先验的对等预测
3. 设计了不可靠指数
4. 仿真结果表明方法有效