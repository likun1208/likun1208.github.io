---
title: 论文记录-Privacy-Preserved Data Disturbance and Truthfulness Verification for Data Trading
date: 2024-2-15 16:18:00
tags:
  - 审稿意见
categories: 论文
description: 审稿记录
---
# Privacy-Preserved Data Disturbance and Truthfulness Verification for Data Trading

## Abstract
- 两种数据交易方式：初始交易和转卖交易
- 面临问题：交易数据的真实性无法通过隐私保护的方式得到保证
	- 数据生成者为了保护隐私而给数据添加的扰动是独立随机的，数据消费者无法在其所要求的扰动参数下验证交易数据是否受到扰动，而不会给数据生成者带来隐私泄露
	- 由于允许转售交易，DC在保护其购买隐私的同时，在数据转售商（DR）的欺骗下很难验证交易数据的来源真实性
- 本文提出：数据交易的隐私保护数据干扰和真实性验证
	- 引入了一个诚实但好奇的交易服务器（TS）来辅助我们设计的隐私可验证印记嵌入式干扰方法，其中印记是盲目的
	- TS 通过构建嵌入印记的个人验证公式，并要求被验证的参与者解密公式结果，来实现自适应真实性验证
	- 被验证的参与者无法通过盲目的印记值来伪造正确的结果，从而确保了所设计验证方法的准确性
- 理论分析和实验表明了有效性

## Intro
- 几个角色定位：
	- 数据消费者DC：广告商、服务提供商等
	- 数据生成者DG
	- 数据转卖者DR：DC买到数据以后可以再卖给其他人
- 允许数据生成者以受干扰的版本发布数据从而保护隐私（我理解的就是差分隐私）
- 数据交易面临挑战：无法以隐私保护的方式同时保证交易数据的干扰真实性和来源真实性
	- DG可能不按扰动参数添加噪声，而是添加更多噪声，而这难以被其他人验证，同时也存在干扰验证方法，会导致数据隐私泄露
	- DR会谎报数据来源以获取更高收益，一些研究通过添加数字水印的方法来确保数据来源可追溯，但带来了较大开销，而基于区块链的方法则暴露了DC的交易行为，带来新的隐私问题。
- 本文：数据交易的隐私保护数据干扰和真实性验证
	- 引入了一个诚实但好奇的交易服务器（TS）来监督DG的独立数据干扰，这使得DG的干扰真实性可验证，同时保护DG的原始数据不被暴露。 
	- TS针对参与者多样化的不真实策略，通过设计的加密验证公式来验证交易数据的扰动性和来源真实性，不会带来任何DG的原始数据隐私成本或DC的购买隐私泄露。
- 贡献：
	- 私人可验证的印记嵌入干扰方法：参考差分隐私，TS生成带验证位的噪声，DG基于隐私检索给数据添加随机噪声
	- 自适应真实性验证方法：TS参考同态加密，构造嵌入印记的个人验证密文公式，并要求验证参与者解密公式结果，同时保留DG的原始数据和DC的购买隐私
	- 理论分析和实验

## 研究现状
略过

- 扰动真实性方面的研究：博弈论来确定扰动参数，但是DG可能不按照这个参数来添加，有研究做了可验证的扰动参数设置，但又带来隐私泄露的问题。也有真值挖掘和对等预测的方法，但数据交易中的真实数据可能不唯一。另一些通过博弈论设计约束机制的研究，但是DG和DC往往利益冲突，无解。
- 数据源真实性方面的研究：
	- 设计执行环境限制原始数据交易：限制交易灵活性
	- 找第三方做相似性比较：可信第三方不好找
	- 区块链：几个研究本身的场景有局限，方法倒是没问题
	- 数字水印：几个研究没设计具体算法

## 问题形式
### 系统模型
- DG生成数据，并按照TS提供的噪声给数据添加扰动
- DC获取指定扰动参数的数据，并借助TS验证扰动和来源的真实性
- DR买了数据再转卖，这一过程中要验证扰动和来源的真实性
- TS负责监管，给TS产生噪声，并自适应地验证其扰动和来源的真实性

整个流程如下：
1. TS生成噪声，DG用加法同态加密结合噪声给数据加密，隐私检索使得噪声对于大家都是未知的
2. TS构造加密验证公式，要求交易参与方解密公式结果，只有扰动和来源都真实的情况下才能得到正确结果
### 问题定义
- $\varepsilon$表示DC希望的隐私参数
- $d$：DG的原始数据
- $\tilde{d}^{(a)}$：DG加扰动后的数据
- $\eta$：添加的扰动噪声（这里感觉是写错了，原文用的是where，但是前面没出现这个字母，而后文扫了一下也没有）
- $O_{\tilde{d}^{(a)}}^{c}$是数据$\tilde{d}^{(a)}$声称的来源，而 $O_{\tilde{d}^{(a)}}^{t}$和 $O_{\tilde{d}^{(a)}}^{u}$分别是真实来源和假来源

存在的恶意行为：
1. DG添加比隐私参数要求更多的噪声
2. DC转卖数据时谎报来源

定义1：隐私保护的扰动真实性的可验证性：在不暴露原数据和噪声的情况下，DC可以验证扰动后的数据=原数据+噪声，也能验证噪声满足差分隐私的要求。

定义2：隐私保护的来源真实性的可验证性：DC收到数据后可以在不暴露交易隐私的情况下验证数据来源。

### 威胁模型
TS是诚实但好奇的第三方：TS会遵循协议设计噪声等，但对DG的原数据感兴趣，甚至会和DC串通获取原数据

TS的攻击方式：泄露原数据、泄露噪声

DG的攻击方式：加更多噪声

DR的攻击方式：谎报数据来源和扰动参数

### 设计目标
1. 交易数据的扰动真实性和来源真实性
2. DG原数据的隐私和DC购买行为的隐私

## 本文方案
1. TS用DC提供的公钥生成一组噪声，每个噪声都带一个验证位，验证的时候，对噪声和验证位分别用DG的公钥加密，然后对两个加密后的数据进行某种运算，就可以得到由扰动参数决定的相关函数$f(\varepsilon)$，运算方式和函数都由TS决定，且每轮都会改变。
2. DG生成$n$维密文向量$\omega$，其中第$k$项$w_k$是对1使用DG公钥加密后的密文，而其他位是对0加密的密文。
3. TS对$\omega$和前面第一条中带验证的噪声执行内积，得到的结果就是DG需要的随机噪声（DG公钥加密版），由此，DG可以用自己的私钥解密得到使用DC公钥加密的噪声，接着DG通过同态加密对数据添加扰动，这样DG并不知道DC要加多少噪声，也不知道噪声具体是什么样的，但实现了指定噪声量的添加。
4. 接下来TS构造一个函数，按照函数的运算结果，第一次卖和转卖算出来的会不一样，从而确保来源可追溯。

概括来说就是分两部分：
1. 干扰嵌入：这部分的功能是实现保护数据隐私和噪声量隐私的前提下实现对数据的扰动嵌入。具体流程其实前面已经介绍两次了，DC提出指定的差分隐私扰动参数，TS基于参数生成噪声+验证位，DG生成密文向量并发给TS，TS对密文向量和噪声+验证位做内积，得到最终添加的扰动，发给DG，DG可以用私钥解密得到噪声+验证位，然后DG将原数据与噪声相加（注意验证位并不加进去）。当然DG可以在这里添加不满足参数要求的噪声。
2. 自适应真实性验证：这部分是实现真实性的验证，如前文所说，包括添加噪声量的真实性和来源的真实性。
	1. 噪声量真实性：简单来说就是构造一个可以用同态加密计算的公式，公式大致是扰动后数据-扰动噪声-原数据+TS随机选的一个数（保密），公式中的每个部分都先用DC公钥加密再用DG公钥加密，TS将公式计算结果先发给DG，DG用私钥解密，解密结果再发给DC，DC再用私钥解密，如果扰动参数没问题，根据同态加密，这样解密完的结果就是TS随机选的那个数。
	2. 来源真实性：计算步骤和前面是一致的，核心思想就是如果这个DG并不是数据的真实来源，那他不可能算出正确结果。

接下来的内容基本上是各功能的证明，以及实验。实验部分用了真实的传感数据，不过自定义了诚实的概率，实验表明本文的方法比baseline更能发现不诚实的交易。



## 最终意见

小修

summary:
The paper presents a novel approach to enhance privacy and truthfulness in data trading. It introduces a system incorporating privacy-preserved data disturbance and truthfulness verification mechanisms to ensure data integrity and confidentiality in trading scenarios. The methodology involves an honest-but-curious trading server to oversee data disturbance, allowing for the verification of data truthfulness while protecting raw data from exposure. The paper also proposes an encrypted verification formula that verifies traded data's truthfulness without imposing privacy costs on participants.

strength:
1. Innovative Approach: The paper's proposal for a privacy-preserved data disturbance mechanism coupled with truthfulness verification is novel and addresses significant concerns in data trading.
2. Privacy Protection: The methodology ensures that the privacy of the data is maintained throughout the trading process, which is crucial for sensitive or personal information.
3. Verification without Privacy Cost: The verification process designed does not add any privacy cost to trading participants, promoting a secure trading environment.

weakness:
- Related work中两部分的内容都是一长段话，建议分类总结一下，增强可读性
- 一些符号和表示方法没有解释，需要补充
- Section IV.B, "Based on the above two modules including the private-verifiable imprint-embedded disturbance and private-verifiable imprint-embedded disturbance,"重复了
- 补充一个问题：$\omega$向量的第k位是用DG公钥加密的1，而其余位是用DG公钥加密的0，那么收到向量的TS只需要对比一下哪一位和其他位不同，就可以判断出哪一位是1了吧，这能起到保密作用吗？还是说没有按位加密而是整个向量一起加密？
