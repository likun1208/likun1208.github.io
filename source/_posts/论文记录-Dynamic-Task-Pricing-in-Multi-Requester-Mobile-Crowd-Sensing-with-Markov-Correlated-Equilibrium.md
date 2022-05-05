---
title: 论文记录-Dynamic Task Pricing in Multi-Requester Mobile Crowd Sensing with Markov Correlated Equilibrium
date: 2020-05-14 11:40:43
tags: 
	- game theory
	- crowd sensing
category: 论文
---

# 基于马尔可夫相关均衡的多发包方移动群体感知中的动态任务定价

<!--more-->

## Introduction

1. 场景：移动群智感知，多个发包方公开收集群体感知数据，公开定价来吸引workers
2. 问题：
   1. requestor之间是不完整信息博弈
   2. 动态系统中未来的不确定性：未来会有多少workers
3. 解决方案：动态定价—>Markov博弈，长期累积效用最大化，近似最小社会成本

## Preliminaries

1. K个requestors，每个用$r_i$表示，集合是R
2. $r_i$发布$N_i$个任务，总共有W种类型，每种类型的任务只能由对应类型的worker来完成
3. $N_{i,w}$表示第i个requestor发布的w类型的任务数量
4. $R_w$表示发布了w类型任务的requestors
5. $N_i$表示所有同一个i的$N_{i,w}$，也就是一个requestor发布的所有任务数量
6. 两种场景：
   1. 所有workers同时到达
   2. workers动态到达



这篇暂时先不往后看了，师弟组会讲过了