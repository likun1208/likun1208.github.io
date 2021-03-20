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

### Overview of Our Algorithm for the Ranking Mode

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