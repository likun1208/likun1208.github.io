---
title: 论文记录-Strategic Information Revelation in Crowdsourcing Systems Without Verification
date: 2020-12-25 09:38:43
tags: 众包
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

### Platform’s Decisions and Payoff

## SOLVING THREE-STAGE MODEL

### Worker Equilibrium Behaviors in Stage III

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