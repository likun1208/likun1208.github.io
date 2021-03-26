---
title: '论文记录-N-in-One: A Novel Location-Based-Service'
date: 2020-07-18 15:51:56
tags: 
	- LBS
	- POI
category: 论文
description: 针对LBS中的POI问题的研究
---

# 新的LBS系统

## Abstract

1. 现有LBS基于单一POI，而现实里用户需要多POI的LBS
2. 本文将单一POI的LBS扩展为使用单个查询请求多个POI的LBS

## Introduction

1. 场景：用户希望同时查询某地点附近的多个兴趣点（例如饭店和KTV都需要）
2. 和单兴趣点推荐的差别：要综合考虑多个兴趣点的评价和距离以及用户需求，例如吃完饭去KTV这种场景就需要推荐的饭店和KTV近一些
3. 本文实现的功能：
   1. Ranking mode（排序模式）：根据每个兴趣点的评价和兴趣点之间的距离，提供一个按评分排序的列表中的前K个兴趣点组合
   2. Service area mode（服务区模式）：找出一个包含了最多兴趣点的矩形区域
4. 挑战性：
   1. 受限于兴趣点之间的距离，实现"N-in-One"与单纯找N个相互独立的兴趣点并不同；
   2. 服务区模式可能会有木桶效应问题：返回的聚类结果可能因为有一个或多个热门兴趣点被排除。
5. 本文提出：
   1. 排序模式：基于平面扫描算法，垂直线从右往左扫描兴趣点区域，遇到兴趣点时回溯和已经记录的兴趣点匹配，以查找最优K聚类；为实现回溯过程中的POI匹配，引入“可查看网络”；
   2. 服务区模式：引入“瓶颈值”来描述 POI 的重复使用数，使用计算几何来识别给定大小的矩形，该矩形可以覆盖尽可能多的最佳Q群集，同时减少木桶效应。
6. 本文组织结构：
   1. 第二部分、第三部分分别介绍排序模式和服务区模式
   2. 第四部分仿真实验
   3. 第五部分相关研究
   4. 第六部分总结

## “N-IN-ONE” LBS IN THE RANKING MODE

### Metric to Evaluate a POI Cluster

POI聚类$\Pi=<p_1,...,p_N>$，其中$p_j$是第$j$个兴趣点位置

**定义1**  POI聚类的直径：覆盖该聚类所有兴趣点的最小圆直径

**定义2**  POI聚类的中心：覆盖该聚类所有兴趣点的最小圆圆心

最小圆问题：直径取决于聚类中距离最远的两点之间的距离上限

**评估函数：**
$$
R(\Pi)=\sum_{j=1}^{N}\alpha_je_j-\beta D(\Pi)-\gamma d(\Pi,c)
$$
其中，$e_j$是对该聚类内第$j$个兴趣点的评估值，$\alpha_j$是对应的权重；$D(\Pi)$是聚类的直径，体现了聚类的离散程度，$\beta$是对应的权重；$d(\Pi,c)$是当前位置$c$到聚类中心的距离，$\gamma$是对应权重。所有权重由用户自己决定。

### Overview of Our Algorithm for the Ranking Mode

用户兴趣区域内有$n$个POI，集合表示为$\Omega$，$||\Omega||=n$

**算法流程**：

1. 所有POI根据x坐标升序排列（即标号大的POI出现在右边）
2. 垂直线从右向左扫描兴趣区域
3. 当垂直线扫到了某个POI，算法会检查之前找到的POI聚类，并将该POI加到之前的聚类中形成新聚类，聚类中所有元素的POI类型在任何时刻都不同
4. 基于新聚类，更新最优K结果
5. 当兴趣区域内的POI都被扫描过时，上述过程结束，最终的最优K结果被推送给用户

**关键问题**：扫描到一个POI时，如何从之前的POI集合中高效识别那些需要被回溯的

**解决方法**：引入可视网络的概念，$N=(\Omega,E)$，$\Omega$是兴趣点集合，E是可视线集合

可视线：由扫描到的POI及其可视点之一决定。当扫描到某个POI $P_i$时，算法根据其可视线回溯L层邻近POI

L层邻近POI：可视网络中，比$P_i$扫描得早、且从$P_i$出发可通过最多L个可视线到达的POI

采用递归方法，整体分为两步：

1. 构建可视网络
2. 移动和回溯

### Constructing the Viewable Network



### Searching Heterogeneous POIs

### Searching the Best K Results in the Ranking Mode

## “N-IN-ONE” LBS IN THE SERVICE AREA MODE

## PERFORMANCE EVALUATION

### Performance Evaluation Based on Synthetic Data

### Performance Evaluation Based on Real-World Data

## RELATED WORK

当前LBS领域的热点研究：基于位置信息的推荐系统，尤其是兴趣点推荐

1. 潜在Dirichlet分配（LDA）
2. 朴素贝叶斯
3. 地理概率分析框架，策略性地考虑多个因素
4. 动态聚类算法识别运动轨迹

路线推荐同样热门

1. 分析用户交通路线来得到两地之间最受欢迎的路线
2. 基于集体知识的路线推荐框架
3. 候选-生成-验证策略得到K近邻轨迹
4. KNN+更多因素的分析
5. LIT前缀挖掘算法
6. 网络优化问题
7. 轨迹大数据

本文和上述内容的共同点：给用户返回多个POI

不同点：本文侧重查询，是LBS的基础服务；上述侧重推荐，是LBS的衍生服务

本文的创新性：现有LBS的基础服务不涉及多POI查询，本文针对基础服务进行了改进

## CONCLUSION AND FUTURE WORK