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
   1. Ranking mode：根据每个兴趣点的评价和兴趣点之间的距离，提供一个按评分排序的列表中的前K个兴趣点组合
   2. Service area mode：找出一个包含了最多兴趣点的矩形区域

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

## CONCLUSION AND FUTURE WORK