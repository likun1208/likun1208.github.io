---
title: 论文记录-ARTHUR GERVAIS论文略读记录
date: 2023-09-25 09:14:36
tags:
  - blockchain
  - defi
categories: 论文
description: arthur gervais的论文略读，主要关注defi的内容，挑几个出来详细看看
---
# 1. A Study of Inline Assembly in Solidity Smart Contracts
这篇内容是关于Solidity内联汇编带来的安全性问题，对内联汇编的使用情况做了定量和定性分析。和DeFi没什么关系，先不看了。
#不读
# 2. $A^2MM$: Mitigating Frontrunning, Transaction Reordering and Consensus Instability in Decentralized Exchanges
大致是实现了一个自动套利做市商，能增强共识安全性、降低sandwich攻击，提供经济利益，释放多出来的空间。可以细看一下。
#精读候选 #自动化交易
# 3. AMR: Autonomous Coin Mixer with Privacy Preserving Reward Distribution
这篇讨论如何通过匿名混币服务实现隐私保护，大致思路是增加匿名集的大小和用户多样性，而用户则为隐私保护而付费，支付形式类似DeFi。这篇要细看。
#精读 #混币
# 4. An Empirical Study of DeFi Liquidations: Incentives, Risks, and Instabilities
这篇是针对DeFi借贷清算系统的研究，细看一下。
#精读 #借贷清算

# 5. Attacking the DeFi Ecosystem with Flash Loans for Fun and Proﬁt
这篇是对闪电贷过程的描述和分析，介绍了过去的几次闪电贷攻击，应该是之前已经有所了解的内容，可以再看看。
#略读 #安全 

# 6. Blockchain Censorship
探讨了区块链审查制度的定义、定量评估、对中立性和安全性的影响以及不同共识对审查制度的限制，可以放后面细看一下。
#精读候选 #审查

# 7. Blockchain Large Language Models
训练区块链大语言模型来检测异常交易。打算大致看一下。
#略读 #异常检测

# 8. CeFi vs. DeFi Comparing Centralized to Decentralized Finance
对cefi和defi比较分析，做了一些概念性的总结，细看。
#精读 #综述

# 9. ConFuzzius A Data Dependency-Aware Hybrid Fuzzer for Smart Contracts
研究智能合约上的混合模糊测试工具，用于避免智能合约代码编写错误导致的各种损失。不看了。
#不读 

# 10. EXPLORING THE ADVANTAGES OF TRANSFORMERS FOR HIGH-FREQUENCY TRADING
关注高频比特币-USDT交易的对数回报预测，研究新型深度学习 Transformers 架构，并将其与传统的长短期记忆模型进行了比较。不看了。
#不读 

# 11. Finding Typing Compiler Bugs (Best Paper + Best Artifact Award)
这篇也是关于编译错误检查的，先不看了。
#不读 

# 12. High-Frequency Trading on Decentralized On-Chain Exchanges
针对去中心化交易所的攻击，主要是sandwich攻击，这篇论文介绍了攻击的形式等，细看一下。
#精读 #自动化交易

# 13. Mitigating Decentralized Finance Liquidations with Reversible Call Options
关注defi的清算系统，引入可逆看涨期权这一金融学的术语，用于defi借贷。可以看一下。
#精读候选 #借贷清算

# 14. On How Zero-Knowledge Proof Blockchain Mixers Improve, and Worsen User Privacy
零知识证明在混币器方面的应用分析。可以看一下。
#精读候选 #混币 

# 15. On the Just-In-Time Discovery of Profit-Generating Transactions in DeFi Protocols
这篇也是做自动套利的，大致看看。
#略读 #自动化交易 

# 16. Quantifying Blockchain Extractable Value: How dark is the forest?
研究三明治攻击、清算和去中心化交易所套利中提取美元来量化 BEV 的危险，细看一下。
#精读 #自动化交易  

# 17. Security of Decentralized Financial Technologies (Dagstuhl Seminar 22421)
DeFi的安全性讨论，细看一下
#精读 #安全

# 18. Smart Contract and DeFi Security Insights from Tool Evaluations and Practitioner Surveys
也是defi和智能合约的安全性讨论，细看一下
#精读 #安全 

# 19. SoK: Decentralized Finance (DeFi) Attacks
对defi攻击的综述，细看。
#精读 #综述 

# 20. Speculative Multipliers on DeFi: Quantifying On-Chain Leverage Risks
关于量化交易、自动做市商的安全性问题讨论，大致看看吧
#略读 #自动化交易 

# 21. The Blockchain Imitation Game
区块链模仿攻击，可以看看。
#精读候选 #安全

# 22. The Eye of Horus: Spotting and Analyzing Attacks on Ethereum Smart Contracts
在本文中，我们提出了 Horus，这是一个基于逻辑驱动和图形驱动的交易分析来自动检测和调查智能合约攻击的框架。略读吧。
#略读 #安全 

# 23. Time to Bribe: Measuring Block Construction Markets
追踪以太坊区块构建过程，探讨存在的风险。大致看看。
#略读 #安全 

# 24. Towards Automated Security Analysis of Smart Contracts based on Execution Property Graph
针对智能合约攻击设计的自动检测方法——执行属性图，我对这个方法有点兴趣，可以看看。
#精读候选 #安全 

# 25. Towards Private On-Chain Algorithmic Trading
自动化交易的机器人，大致看看。
#略读 #自动化交易 

# 阅读规划
精读
1. CeFi vs. DeFi Comparing Centralized to Decentralized Finance
2. Smart Contract and DeFi Security Insights from Tool Evaluations and Practitioner Surveys
3. An Empirical Study of DeFi Liquidations: Incentives, Risks, and Instabilities
4. AMR: Autonomous Coin Mixer with Privacy Preserving Reward Distribution
5. High-Frequency Trading on Decentralized On-Chain Exchanges
6. Quantifying Blockchain Extractable Value: How dark is the forest?
7. Security of Decentralized Financial Technologies (Dagstuhl Seminar 22421)
8. Smart Contract and DeFi Security Insights from Tool Evaluations and Practitioner Surveys

精读候选
1. Towards Automated Security Analysis of Smart Contracts based on Execution Property Graph
2. The Blockchain Imitation Game
3. Blockchain Censorship
4. Mitigating Decentralized Finance Liquidations with Reversible Call Options
5. On How Zero-Knowledge Proof Blockchain Mixers Improve, and Worsen User Privacy
6. $A^2MM$: Mitigating Frontrunning, Transaction Reordering and Consensus Instability in Decentralized Exchanges

略读
1. Attacking the DeFi Ecosystem with Flash Loans for Fun and Proﬁt
2. The Eye of Horus: Spotting and Analyzing Attacks on Ethereum Smart Contracts
3. Time to Bribe: Measuring Block Construction Markets
4. On the Just-In-Time Discovery of Profit-Generating Transactions in DeFi Protocols
5. Speculative Multipliers on DeFi: Quantifying On-Chain Leverage Risks
6. Towards Private On-Chain Algorithmic Trading
7. Blockchain Large Language Models