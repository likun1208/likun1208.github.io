---
title: 论文记录-Causal Intervention for Leveraging Popularity Bias in Recommendation
date: 2023-03-27 19:06:05
tags:
    - 因果推断
    - 推荐系统
categories: 论文
description: 因果推断应用于推荐系统处理偏见问题
---
# Causal Intervention for Leveraging Popularity Bias in Recommendation

## Abstract
推荐系统通常面临着人气偏差的问题：从数据的角度来看，项目在交互频率上表现出不均匀（通常是长尾）的分布；从方法的角度来看，协同过滤方法容易通过过度推荐热门项目来放大偏差。毫无疑问，在推荐系统中考虑人气偏差是非常关键的，现有的工作主要是通过基于倾向的无偏向学习或因果embedding来消除偏差效应。然而，我们认为，数据中并非所有的偏见都是坏的，也就是说，有些项目因为其更好的内在质量而表现出更高的人气。盲目追求无偏见的学习可能会删除数据中的有益模式，降低推荐的准确性和用户满意度。

这项工作研究了推荐中的一个未被探索的问题——如何利用流行度偏差来提高推荐的准确性。关键在于两个方面：如何在训练过程中消除人气偏差的不良影响，以及如何在生成顶级𝐾推荐的推理阶段注入所需的人气偏差。这就对推荐生成过程的因果机制提出了质疑。沿着这个思路，我们发现物品流行度在暴露的物品和观察到的交互作用之间起到了混淆者的作用，造成了偏差放大的坏影响。为了实现我们的目标，我们提出了一种新的训练和推理范式，命名为 "人气-偏向解惑和调整"（PDA）。它在模型训练中去除混杂的流行偏向，并通过因果干预调整具有理想流行偏向的推荐分数。我们在潜伏因子模型上展示了新的范式，并在葵、豆瓣和腾讯的三个真实世界数据集上进行了广泛的实验。实证研究验证了去中心化的训练有助于发现用户的真实兴趣，而带有流行偏向的推理调整可以进一步提高推荐精度。我们在[Github](https://github.com/zyang1580/PDA) 上发布了我们的代码。

## Intro
推荐系统面临人气偏差问题，现有的关于流行偏向感知推荐的工作主要是进行无偏向学习或排名调整，可以分为：
1. 逆向倾向评分（IPS），通过对模型训练中的交互实例进行重新加权，将数据分布调整为均匀[17, 38]。虽然IPS方法有良好的理论基础，但由于估计倾向性的困难和模型的高方差，它们在实践中几乎没有效果。
2. 因果embedding，它使用无偏的统一数据来指导模型学习无偏的embedding[8, 27]，迫使模型放弃商品流行度。然而，获得这样的统一数据需要将商品随机地推荐给用户，这有可能会伤害用户体验。因此，数据通常是小规模的，使得学习的稳定性降低。
3. 排名调整，对推荐列表进行事后的重新排名[3, 50]或对训练进行模型正则化[2, 50]。这两类方法都是启发式设计，有意提高不太流行的商品的分数，然而这些方法的有效性缺乏理论基础。

我们认为，推荐系统不应该消除人气偏差的影响，而应该利用人气偏差。我们的考虑是，并不是所有数据中的人气偏差都意味着坏的影响。例如，有些商品表现出更高的流行度，是因为它们有更好的内在质量或代表了当前的趋势，值得更多的推荐。盲目地消除流行偏见的影响，会失去数据中隐含的一些重要信号，不适当地压制高质量或流行的项目。此外，一些平台有必要在系统中引入期望的偏见，例如，推广那些有可能在未来成为流行的物品。这项工作旨在填补有效利用人气偏差来提高推荐准确性的研究空白。

![图1](https://github.com/likun1208/image/blob/master/ci-1.png?raw=true)

为了理解商品流行度如何影响推荐过程，我们先借助因果图[35]的语言进行定性分析。图1(a)表明，传统的方法主要是进行用户-商品匹配来预测亲和力得分：$U$（用户节点）和$I$（物品节点）是原因，而$C$是结果节点，表示交互率。一个例子是隐语义模型[18, 32]，它将预测形成为用户embedding和项目embedding之间的内积。由于一个模型如何形成预测意味着它假设标记的数据是如何产生的，这个因果图也可以解释观察到的交互数据的假设产生过程。商品流行度虽然对数据生成过程有很大影响，但这种粗粒度的建模方法没有明确考虑。

我们接下来考虑商品的流行度是如何影响这个过程的，从而丰富了 图1(b)中的因果图。节点Z表示商品流行度，有两条边指向$C$和$I$。首先，从Z到C的边表示商品流行度对交互率的直接影响，因为很多用户有从众心理，因此倾向于跟随大多数人消费流行的物品[28, 48]。其次，从Z到I的边意味着商品的流行度会影响商品是否被更多推荐，因为推荐系统通常会继承数据中的偏差，更频繁地推荐流行的商品。值得注意的是，我们发现$Z$是导致$I$和$C$的共同原因，在被推荐的项目和观察到的交互率之间充当混淆因素[35]。这意味着，流行度$Z$通过两条因果路径影响观察到的流行数据：1）$Z\rightarrow C$和2）$Z\rightarrow I \rightarrow C$，其中第二条路径包含偏见放大的负面影响，因为它增加了热门商品的交互率，尽管它们可能不太符合用户兴趣。

为了移除这样的负面影响，我们需要干预推荐商品I，使他们对自己的人气Z免疫。在实验上，这意味着我们需要改变曝光策略，使其不受项目流行度的影响，然后重新收集数据，然而这对学术界的研究人员来说成本很高，不可能实现。由于因果科学的进步，我们可以用do-calculus[35]实现同样的结果，而无需进行干预性实验。简而言之，在训练阶段，与通过那些将用户的兴趣与流行的偏见混为一谈的现有推荐模型估计得到的相关性$P(C|U,I)$不同，我们截断路径$P(C|U,I)$来估计用户-商品的匹配$P(C|do(U,I))$。通过这样的非混淆的培训，$P(C|do(U,I))$比$P(C|U,I)$能更准确地估计用户对商品的兴趣匹配，消除了由于Z的混淆因素而在I和C之间的虚假相关性。在推断阶段，我们推断排名分数$P(C|do(U,I),do(Z))$，用我们期望的偏见来干预项目的流行度𝑍（例如，在测试阶段中的预测流行度）。

本文主要贡献如下：
1. 我们用因果图分析了人气偏差是如何影响推荐系统的，因果图是一个强大的工具，但在社区中很少使用；我们发现，人气偏差的不良影响源于项目人气的混杂效应。
2. 我们提出了 "人气偏差解构和调整"（PDA），这是一个新颖的框架，用do-calculus进行解构训练，并在推荐推理中对人气偏差进行因果干预。
3. 我们在三个真实的数据集上进行了广泛的实验，验证了我们因果分析和方法的有效性。

## Primary Knowledge
我们用大写字母表示随机变量，小写字母表示具体的值，手写体字母表示相应随机变量的样本空间，以及$P(\cdot)$表示随机变量的概率分布。

$\mathcal{D}$：历史数据，通过$T$阶段依次收集得到，即$\mathcal{D}=\lbrace \mathcal{D}_1\cup ... \cup \mathcal{D}_T \rbrace$

$\mathcal{U}=\lbrace u_1,...,u_{|\mathcal{U}|} \rbrace$：所有用户

$\mathcal{I}=\lbrace i_1,...,i_{|\mathcal{I}|} \rbrace$：所有商品

通过对历史数据的学习，推荐系统有望抓住用户的偏好，为下一阶段𝑇+1提供良好的服务。也就是说，它的目标是在$\mathcal{D}_{T+1}$上获得高的推荐精度。本文中我们关注对商品流行度这一因素，将商品$i$在阶段$t$时的局部流行度定义为：$m_i^t=D_i^t / \sum_{j \in I} D_j^t$，其中$D_i^t$表示商品$i$在$\mathcal{D}_{t}$中观察到的交互次数。我们同样可以根据商品在$\mathcal{D}$中的交互频率来定义商品的全局流行度$m_i$，但我们认为局部流行度对系统的曝光机制和用户决策的影响更大，因为系统通常会定期重新训练，最新的数据影响最大。

流行度漂移。直观地说，商品流行度是动态的，并随时间变化，这意味着流行度偏差的影响也可能是动态的。为了量化人气漂移，我们定义了一个名为人气漂移（Drift of Popularity, DP）的指标来衡量两个阶段之间的漂移。首先，我们将每个阶段$𝑡$表示为商品的概率分布：$[m_1^t,...,m^t_{|\mathcal{T}|}]$，其中每个条目表示一个商品在该阶段的频率。然后，我们使用Jensen-Shannon散度(JSD）[15]来衡量两个阶段之间的相似性：$DP(t,s)=JSD\left(\left[m_1^t, \ldots, m_{|I|}^t\right],\left[m_1^s, \ldots, m_{|I|}^s\right]\right)$，其中，$t$和$s$是两个阶段。与JSD类似，DP的范围是$[0, 𝑙𝑜𝑔(2)]$，数值越大，说明流行度越大。

补充说明：JS散度度量了两个概率分布的相似度，是KL散度的变体，JS散度解决了KL散度非对称的问题。一般地，JS散度是对称的，其取值是0到1之间，具体参考[这个链接](https://blog.csdn.net/hy592070616/article/details/122387046)。

![图2](https://github.com/likun1208/image/blob/master/ci-2.png?raw=true)


图2(a)显示了三个真实世界数据集上连续两个阶段的DP值，即$DP(t, t +1)$，其中$t$从1到9迭代（详见4.1.1节）。我们可以看到，三个数据集都明显存在流行度漂移，不同的数据集表现出不同的流行度漂移程度。图2(b)显示了第一阶段和现阶段的DP值，即$DP(1,t )$，它衡量了累积的人气漂移。我们可以看到一个明显的上升趋势，表明时间间隔越长，数据表现出的人气漂移越大。这些结果显示，人气偏差及其影响也会随着时间的推移而变化。未来阶段的人气偏差与过去阶段的人气偏差不同。如果我们把模型泛化的目标设定为在下一阶段的数据$\mathcal{D}_{T+1}$上追求高准确度，那么一个可行的方法就是预测流行趋势并将其注入到推荐中。

## METHODOLOGY

### Causal View of Recommendation

### Deconfounded Training

### Adjusting Popularity Bias in Inference

### Comparison with Correlation $P(C|U,I)$

## EXPERIMENTS

### Experimental Settings

### Deconfounding Performance (RQ1)

### Performance of Adjusting Popularity (RQ2)

## RELATED WORKS

## CONCLUSION




