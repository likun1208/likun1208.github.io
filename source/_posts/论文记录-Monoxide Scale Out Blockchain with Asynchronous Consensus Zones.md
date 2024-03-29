---
title: '论文记录-Monoxide: Scale Out Blockchain with Asynchronous Consensus Zones'
date: 2020-04-07 18:38:45
tags: blockchain
category: 论文
---
## 突破区块链不可能三角异步共识组

这篇没看完。

<!--more-->

### 摘要

1. 背景：加密货币在匿名在线支付方面很重要
2. 问题：
   1. 随用户和交易的增长，低吞吐量阻碍加密货币系统的扩展
   2. 每个节点复制整个网络的通信、存储和状态表示也阻碍了系统的扩展
3. 本文：
   1. Asynchronous Consensus Zones（异步一致网络）：分区域，不影响去中心化和安全性，每个区域独立并行，块内共识；
   2. 诸葛连弩系统：使得攻击单独的一个块的难度和攻击整个区块的难度一样大

### Introduction

1. TPS（每秒交易数）很低的原因在于区块按顺序建立，而网络延迟导致广播时间不能过短
2. 每一个完整的节点都需要复制整个网络的通信、存储和状态表示，系统受限于硬件设备而无法扩展
3. 综上可扩展的区块链要考虑：
   1. 共识协议的可扩展性
   2. 通信、存储、计算和内存的资源使用
   3. 去中心化和安全性
4. 以太坊已提出分片的设计
5. 本文异步一致网络：
   1. 分区域，通过在多个独立和并行实例(或一致区域)中划分和处理工作负载来扩展区块链系统
   2. 整个网络划分为区域，每个区域负责自己那部分，只在区域内复制，挖矿竞争和交易确认等在区块间异步进行
   3. 挑战在于：
      1. 跨区域事务的吞吐量
      2. 诚实矿工稀释带来的安全性问题
6. 本文最终原子确认：跨区域事务由原子性分解为多个事务，每个事务涉及单独一个区域
7. 本文诸葛连弩：矿工挖矿成功时可以在不同的区域里创建多个区块，其算力平均分配到各个区域，而攻击者的攻击算力同样也被分到了不同区域，因此攻击单独一个区域和攻击整个系统难度一样

### Background

对比PoS和PoW，UTXO和账户余额

1. 影响区块链系统和共识协议性能的因素：一致性（共识）、通信、存储、表示
2. PoW耗费算力来竞争记账权，但是加密货币一定程度上有对应现实世界的价值；PoS由节点的股权（财富）来决定区块的产生；本文用PoW，但改PoS也行
3. 加密货币有两种交易模型，UTXO和账户余额，以太坊用了后者（更支持智能合约），本文也用了后者，总之账户余额模型有很多优点

### System Design

1. 跨区域事务的执行：
   
   1. 区域A执行检查余额然后取钱（ withdraw operation），下一个该区域的区块就会更新余额状态等
   2. 区域A把存钱（ deposit operation ）打包成一个中继操作（ relay transaction ）发给区域B
   3. 区域B执行存钱操作，不需要检查余额
   4. 取钱只涉及区域A，存钱只涉及区域B，因此不需要进行区域间的确认等

2. 划分和命名（Partitioning and Naming）：
   
   1. 用户地址被均匀划分为$2^k$个区域，一个区域由它的分片尺度k和区域索引s来识别
   2. 群swarm：参与相同数据集的复制的一组节点。全节点加入群从而广播信息和接收其他全节点的信息。比特币和以太坊系统只有一个群，大家都保存同样的信息。
   3. 本文有多个出于不同目的建立的群，采用分布式哈希表(DHT)进行群寻址和对等发现。
   4. 系统中有一个包含所有全节点的全局群，用于复制所有区域的最小公共信息。
   5. 在每个群内，全节点稀疏分布，使用gossip protocol广播信息
   6. 区域特定的群也用分片尺度k和区域索引s来识别

3. 单独一个区域：区域间计算和事务相互隔离、独立并行，随网络增长会创建新区域
   
   1. 全节点或矿工有特定标识符来决定在哪个区域内工作
   2. 每个区域有自己的区块链
   3. 矿工只与同区域的PoW竞争记账权
   4. 全节点会忽略其他区域的事务消息

4. 最小化跨区域开销：
   
   1. 系统在每个节点上维护一个分布式哈希表(DHT)
   2. 大多数通信用于复制未经确认的事务，并广播带有已确认事务的新块
   3. 获取未确认事务或新块对应的区域索引s后，系统在DHT中选择索引相同的节点，通过gossip protocol把事务和区块发给它们。
   4. 只把中继事务发给对应区域，而不是全网广播
   5. 用于形成链的最小化数据会被复制到所有区域（下一部分讨论）
   
   ### Efﬁcient Cross-Zone Atomicity
   
   1. 事务分为取钱（relay）和存钱（deposit）操作，分别在对应的区域进行验证
   2. 为避免分叉出现的问题：
      1. 取钱验证一次
      2. 存钱验证一次
      3. 中继操作隔一定数量的区块后才被打包
   
   ### Defense Per-Zone Security
   
   1. 允许矿工完成一次PoW在多个区域建立多个区块（区块网？），每个区域只能建立一个
   2. 参与诸葛连弩的矿工的算力没被稀释，这样攻击单独一个区域所需的算力仍然是50%
