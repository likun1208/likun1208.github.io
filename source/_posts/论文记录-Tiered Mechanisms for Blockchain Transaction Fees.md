---
title: 论文记录-Tiered Mechanisms for Blockchain Transaction Fees
date: 2023-11-7 15:05:40
tags:
  - blockchain
  - defi
categories: 论文
description: 交易费分级机制
---
# Tiered Mechanisms for Blockchain Transaction Fees
## 摘要
1. 证明了以太坊目前使用的交易费机制 EIP-1559 并不具有包容性，并通过实验证明其价格在拥堵期间会横向飙升
2. 引入了一个能够捕捉区块链系统流量多样性的模型，以及一个能够实施更具包容性的交易政策的分级定价机制
3. 从形式上证明了我们的机制可以实现稳定的预期价格，我们还提供了实验结果，证明对于低紧迫性交易来说，交易价格可以保持在较低水平，从而使进入区块链的交易类型多样化
4. 并不一定会牺牲收益，因为由于机制的价格歧视能力，低紧急交易价格的降低可以从高紧急交易中得到补偿

## Intro
1. 背景：用户的收益与交易上链时间相关，不同类型应用的交易有不同的紧迫性要求，例如在一个Layer 2支付系统中，DeFi交易更紧急，而结算交易紧迫性较低。理想的交易收费机制自然应该能满足各种应用对不同紧迫性的要求。
2. 现状：主流交易费方法以价格拍卖为主，这导致网络拥塞时，紧急交易加价，连带非紧急交易也需要加价，这样的系统有失公平。
3. 问题：让用户指定交易的紧迫度不现实，还需要考虑沟通成本、激励设计等
4. 本文：分层交易方案
	1. 动态确定层的数量、每一层的延迟和价格，用户自行选择去哪一层
	2. 提出了一种新型模型，用于捕捉客户对区块链处理的需求，该模型利用单调递减函数将延迟映射为价值，概括一下就是关注了交易延迟导致的用户收益下降
5. 目标：当系统遇到拥堵时，有一种政策可以在多种类型的用户之间平衡服务，由此考虑符合公开共享和商定的多样性政策的交易费机制，例如规定平均将一个区块中 20% 的交易空间用于低急迫性交易
6. 优势：从流量多样性的角度对分层机制进行了分析，并证明在 "无点质量 "分布的标准假设和预期需求连续的合理假设下，分层机制可以实施丰富多样的政策
7. 实验：研究分层机制如何收敛到一组价格，以及在需求波动时的表现
	1. 研究了需求激增超过网络容量一段时间的重要情况
	2. 与 EIP-1559 协议进行了比较
	3. 实验结论：
		1. 价值不会随时间显著下降的交易（也就是低紧迫度的交易）价格可以保持在较低水平，从而使进入区块链的交易类型多样化，并根据紧急程度进行适当的价格区分。
		2. EIP-1559 中的价格在整个网络拥堵期间横向飙升。
		3. 研究了系统对收入的影响，证明分层定价没牺牲收益。

## Preliminaries
### 区块链稳态
1. 用户所提交交易的特点：具有随延迟而变化的价值，每个交易都与一个从延迟到价值的映射的函数$v_i: R_{\geq 1}\rightarrow R_{\geq 0}$有关，延迟最小是1，表示交易在下一个块就上链。如果$d\lt d'$，则$v(d)\gt v(d')$，意味着延迟越小价值越高。
2. 区块链吞吐量：B，衡量一个块里有多少交易
3. 进入区块链系统的交易价值函数独立同分布，服从分布F，单位区块的交易到达率是$n$，为了模拟高持续需求，这里假设$n\gt B$
4. 区块链交易机制$M(v_i|n,F)=(d,p)$是从交易到价格和延迟的映射，机制与用户身份无关
5. 给定价值是$v$的交易，其收益是$u(v|M,n,F)=v(d)-p$，也就是延迟之后的交易价值减去为了让交易上链而需要付的价格
6. 需求约束：$D=\{v\in supp(F)|v(d)−p\geq 0 for (d,p)=M(v|n,F)\}$
7. 收益非负的交易会以略低于吞吐量的速率到达，$n\cdot Pr[D]\leq B$
8. 例1：机制1：以太坊的机制例子（EIP-1559）：将前一个区块$p$的价格及其满载率$f$作为输入，并输出新的价格，计算公式：$p(1+\frac{1}{8}\frac{f-targetLoad}{targetLoad})$
9. 为了满足需求约束，机制所提供的价格$p_0$必须使得：$B=n\cdot Pr[D]=n\cdot Pr[v(0)\geq p_0]$，由此，机制转变为：$M(v|F)=(0,p_0)$
10. 定义1：稳态近似真实性：对于所有的$n,F,v,v'\neq v$，都有$v(d)−p\geq v(d')−p'$，其中$(d,p)=M(v|F)$且$(d',p')=M(v'|F)$，则称机制M满足真实性。
11. 命题1：稳态近似真实性特征：当且仅当对于所有的$n,F,v_1,v_2$，$d_1\geq d_2\Rightarrow p_1\leq p_2$时机制M是真实的，$M(v_1|F)=(d_1,p_1)$且$M(v_2|F)=(d_2,p_2)$。此外，如果$d_1=d_2$则$p_1=p_2$。
12. 我的问题：交易价值函数服从的分布F，和负载F是一回事吗？分布为什么会变成负载？
	1. $n$表示每个区块新生成的交易数量。它反映了进入区块链的交易流量和需求。
	2. $F$表示交易价值函数$v$的分布。它描述了不同交易的价值函数$v$以何种概率分布出现。
	3. $(n,F)$表示每隔一个区块有n个新交易生成，这些交易的价值函数v按照分布F进行独立同分布抽样。即n控制交易流量，F控制交易类型组成比例。它们一起描述了区块链面对的总体交易负载情况。

## Traffic Diversity
1. 现有机制倾向于满足具有特定紧迫性要求的交易，而没能让不同紧急程度的交易有效利用平台。
2. 本文引入机制应实施的多样性策略概念来抽象定义什么是多样性的机制，不涉及策略的具体选择。
3. 通过检查交易价值在适当延迟后是否仍高于阈值来区分紧迫度，直观来说，延迟增加时，高紧迫度的交易价值会迅速下降，而低紧迫度则不会。
4. 多样性策略由一组条款组成，这些条款描述了区块链中包含的交易$a_i$的百分比，这些交易在特定延迟$d_i$下的价值超过了某个阈值$p_i$。
5. 定义2：多样化策略：一个多样化策略P是一个集合$\{(a_i,d_i,p_i)\}_{i\in N}$，其中$a_i,d_i,p_i$分别属于$(0,1],R_{\geq 1},(0,1]$。P中的三元组$(a_i,d_i,p_i)$被称为策略条款。
6. 定义3：执行：一个机制M在负载F执行了一个多样性策略$P:=\{(a_i,d_i,p_i)\}_{i\in N}$当且仅当对于所有$i\in N$，$E[|\{j|u(v_j|M,F)\geq v_j(d_i)-p_i\gt 0,(v_1,...,v_n)\leftarrow F\}|]\geq a_i\cdot B$都成立。
7. 例2：给定F，EIP-1559 机制会找到一个值$p$，即当延迟等于 1 时，在期望值上正好有B个交易的价值至少为$p$。因此，它在负载F下执行策略$P:=\{(1, 1, p)\}$（假设 p 存在）。

## A Diverse Mechanism
### EIP-1559不满足多样性
1. 机制设计者希望机制能够满足包含某些策略条款$(a,d,p)$的策略P，其中$a\gt 0,d\gt 1$
2. 在给定$(a, d, p)$的情况下，存在一个具有两个紧迫度的价值分布F（如下图），EIP-1559 在$(a, d, p)\in P$的情况下不执行任何策略 P。主要原因是会优先选择小费更高的交易，因而忽略了小费较低的不那么紧迫的交易。
	![image](https://github.com/likun1208/image/blob/master/tiered-1.png?raw=true)
3. 定义4：图里的负载分布定义：给定参数$a,d,p$，使得$d\gt 1,a,p\gt 0$
	1. 0.5的概率，F输出价值函数$v(1):=2p$和$v(d):=0.2p$
	2. 0.5的概率，F输出价值函数$v(1):=\frac{3p}{2}$和$v(d):=p$
	3. 两个函数都是单调的
4. 命题2：令$(a,d,p)$作为一个策略条款，其中$d\gt 1,a,p\gt 0$，在前述负载F下，EIP-1559不会实现任何包含$(a,d,p)$的策略P。
	证明：按我的理解，这里意思是说，网络拥塞的时候，交易很多，EIP-1559就会把交易费上限设置为接近2p的价格，尽可能打包更多交易，通过提高交易费来试图降低交易量，这样价格较低的第二类交易就不可能被选中。而第一类交易在延迟为d时的价值是0.2p，并不满足策略条款。

### 分层定价机制
1. 将区块内可用空间划分为最多$k$层，每层有各自的延迟$d_i,i\in[k]$和价格$p_i,i\in[k]$，随延迟增加价格下降。第一层的延迟固定为1，是最小延迟，意味着下一块就上链，其他层的延迟和所有层的价格由一组参数控制动态变化。
2. 机制设计目标：该尽量选择最小的满足约束的延迟和价格，同时尽量增加层级数量和使用率。
3. 延迟和价格的计算方式：
	$$ 
	d_{j+1}\geq \lambda_jd_j,\lambda_j\gt 1,p_{j+1}\leq \mu_jd_j,\mu_j\gt 1\tag{1}
	$$
	其中$\lambda_j,\mu_j,j\in[k-1]$是机制的参数。当约束不满足时，机制回改变至少一个层参数（延迟，价格，层数）从而满足约束。
1. 初始状态下，机制只有大小为B的一层。
2. 每次生成新区块时，会根据上一个块的负载调整不同层级的价格。
3. 不同层的延迟变化频率较低（每过dFreq个块，改变一次延迟），以便价格调整能跟得上负载变化。延迟的变化基于连续层价格的关系，如果$p_{i+1}\gt \mu_i-p_i$，则第$i+1$层的延迟就增加，从而使得$p_{i+1}$减小，来满足约束。否则，$d_{i+1}\geq \lambda_i-d_i$，第$i+1$层的延迟会以一定概率减少1，来避免不必要的延迟，提高系统效率。
4. 层的数量变化的频率更低（每tFreq个块改一次），如果最后一层的价值大于$addTierPrice$，且当前层数$m$小于$k$，就会新增一个尺寸为$a_{m+1}B$的层，价格是$newTierPrice$，延迟是$\lambda_md_m$。第$m+1$层的空间是从第1层挪出来的。类似的，如果最后一层的价格小于$removeTierPrice$，且当前层数大于1，则删去最后一层并把空间还给第一层。
5. 延迟惩罚：为了避免用户加入最便宜的层级然后谎报偏好，机制通过延迟处理包含在层级中的交易来实现惩罚，延迟量为层级的延迟量，也就是说，就所有分类账而言，在产生的区块的时间戳超过包含区块的延迟量之前，交易将被忽略。因此，添加到层级中的交易需要支付与层级价格相等的费用。

## Tiered Pricing is Diverse
### Tiered Transaction Mechanisms
1. 区块分为$\{0,1,2,...,k\}$个层，除了第0层，每层的尺寸是$B=(B_1,B_2,...,B_k)$，延迟是$d=(d_1,d_2,...,d_k)$，所有层的尺寸加起来是B，B表示区块吞吐量。
2. 第0层是特殊的，用来存放没上链的交易，尺寸$B_0=\infty$。
3. 索引越大，延迟越高，服务越差。
4. $n\geq B$是新交易的数量，新交易为$\{t_1,t_2,...,t_n\}$，对于交易$t_i$，如果它被放到第$j$层且$j\neq 0$，则效用为：$u(i,j)=v_i(d_j)-p_j,0\lt j\leq k$，如果$j=0$则$u(i,j)=0$。收益反映出每个交易仅在ddl之前上链才能产生价值。
5. 给定延迟d和价格p，交易$i$的效用记作$u_i(d,p)$。
6. 元组$(B,d,p)$表示某特定区块链。
7. 分层交易机制M将交易分配到不同层使得总效用最大。可能存在两层效用相同的情况，这时交易随机挑一层分。$X_{ij}$表示交易$i$被分到第$j$层，给定交易$i$的期望效用最大化层级随机集合$D_i=\{j\in\{0\}\cup[k]|u(t_i,j)\geq u(t_i,j') \forall j'\}$，我们可以定义$X_{ij}$的分布：
$$ 
	X_{i j}=\left\{\begin{array}{ll}
1 \text { with probability } \frac{B_j}{\sum_{j \in D_i} B_j} & \text { if } j \in D_i \\
0 & \text { otherwise }
\end{array} .\right.\tag{5}
$$

8. 在确定了交易如何选择层级之后，我们将每个层级的需求量（或已用空间）定义为

$$
	T_j=\sum_{i \in[n]} X_{i j}\tag{6}
$$

除了第0层外，每层预期需求量最多应与所分配的层级规模相当
	
$$
	\mathbb{E}\left[T_j\right] \leq B_j\tag{7}
$$

而第0层的需求量是	
	$$
	\mathbb{E}\left[T_0\right]=n-\sum_{j \in[k]} \mathbb{E}\left[T_j\right]\tag{8}
	$$

### Distributional Assumptions
1. 定义5：需求正则：如果生成的价值可以参数化为元组$(v_0,h)$，则价值函数分布F是需求正则的，其中$v_0\in[0,1]$是无延迟时的价值，$h:R_{\geq 1}\rightarrow [0,1]$是折扣因子函数，形式为$v(d|v_0,h)=v_0h(d)$。同时需要以下条件成立：
	1. $h(d)$连续且单调减
	2. $lim_{d\rightarrow\infty}h(d)=0$
	3. $v_0$的边缘分布的概率密度函数始终存在，其定义为$f\left(v_0 \mid h\right)=\frac{d \operatorname{Pr}\left[v \leq v_0 \mid h\right]}{d v_0}$
	4. 对于$v_0\in[0,1]$，$f(v_0|h)\gt 0$
2. 引理1：对于任意区块链$(B,d,p)$和正则需求$n,F$，$E[T_i]$在p和d中是连续的，即便去掉最后一个假设这个引理也是成立的。

### Compatible Blockchains
1. 定义6：兼容：如果公式7（期望需求小于层尺寸）对于所有层都成立，则称区块链$(B,d,p)$在负载n和F下是兼容的。
2. 定义7：EIP-1559稳定区块链：如果一个区块链$(B,d,p)$在负载$n,F$下是兼容的，且每一层都满足
	$$
	\frac{\mathbb{E}\left[T_j\right]}{B_j}\lt 1 \Rightarrow p_j=0\tag{9}
	$$
	则称其是稳定的。
3. 观察1：假设$B_1=B_2=1,d_1=0,d_2=1,n=3$，F是：
	- 概率$\frac{1}{3}$，延迟小于1时，$v_0$服从$[0,1]$的均匀分布，否则$v_0=0$。
	- 概率$\frac{2}{3}$，$v_0$服从$[0,1]$的均匀分布。
	
	如果$p_1\neq p_2$，则$p_1\gt p_2$。否则所有交易都会去选择$B_1$，这样$B_2$就是空的，而根据定义7，$p_2=0$，那么$p_1\lt p_2=0$就与$p_1$非负矛盾了。由此得出两个选项：
	- $p_1\gt p_2$。这种情况下，所有d=2的交易都会选择支付$p_2$，为了实现稳定，就需要$p_2\gt 0.5$。但是$p_1\gt p_2\gt 0.5$对于填充$B_1$有些大了。
	- $p_1=p_2$。这种情况，$B_1$的需求比$B_2$高，因为它会被所有d=1的交易和一半d=2的交易选择。因为$B_1=B_2$，那么在$p_1=p_2$的情况下不可能让交易均匀填充两个区块。因为未填满的区块价格需要设置为0，所以$p_1=p_2\gt 0$是不可能出现的。
4. 总结：
	1. 兼容意思是区块链的交易处理能力可以满足交易的需求量，具体就是每个区块$j$的交易需求量$E[T_j]$小于等于该区块的处理上限$B_j$。这保证了区块链可以流畅运行,不会积压交易造成拥堵。
	2. 在兼容的基础上，又满足如果某个区块j的需求量比上限低,则价格$p_j=0$。这反映了EIP-1559的目标——在降低费用的同时,也要最大化区块空间的利用率，不浪费区块资源。
	3. 观察1中的分布函数F不连续和大于0，不满足前面分布假设中的要求，所以不稳定。

### Properties of Compatible and EIP-1559 Stable blockchains
1. 命题3：有着EIP-1559的以太坊在任何负载下都是兼容的。特别的，对于任意$B,n,F$，都存在价格p使得$(B,d,p)$在负载$n,F$下是兼容的。
	证明：在以太坊中，$B=B_1,d_1=0$，显然根据定义5，$p_1=0$可得$E[T_1]=n$。因为$p_1\gt 1$可得$E[T_1]=0$（因为每个交易的最大效用是1），且$E[T_1]$在$p_1$是连续的，所以存在$p_1\gt 0$使得$E [Tj]=B\lt n$，以满足定义6。
2. 兼容性和 EIP-1559 稳定性的结合意味着更直观的属性。
3. 命题4：对于在需求$n,F$下兼容且EIP-1559稳定的任意区块链$(B,d,p)$，对于所有$j\in[k]$，$E[T_j]=B_j$都成立。	
	证明：如果对于某些层$j$而言， $E[T_j]\lt B_j$ （这是可能兼容的），EIP-1559稳定的性质会使得该层的价格为0。然而，在这种情况下，任何交易都会因加入该层而产生正效用。因为 $n\gt \sum_{j\in[k]}B_j$，这一层会至少有 $n-(\sum_{l\in[k]}B_l-B_j)\gt B_j$ 的需求，因此并不兼容。
4. 引理2：对于任意区块链$(B,d,p)$，和正则需求$n,F$，如果$p_j=1$，则$E[T_j]=0$。
	
证明：
	
$$
\begin{aligned}
\mathbb{E}\left[T_j\right]=n \cdot \operatorname{Pr}\left[t \in T_j\right] & \leq n \cdot \operatorname{Pr}\left[v_0 \cdot h\left(d_j\right)-p_j \geq 0\right] \\
& \leq n \cdot \operatorname{Pr}\left[v_0 \geq p_j\right] \\
& =0
\end{aligned}
$$
其中第一个不等式：进入第$j$层的交易效用必须非负。最后一个等式：两个分布假设决定（即，$v_0$分布在$[0,1]$中，并且等于1的概率为零）。

5. 引理3：对于任意区块链$(B,d,p)$，和正则需求$n,F$，如果 $p_j=0$，则 $\sum_{j\in[k]}E[T_j]\lt B$。

证明：如果$p_j=0$，几乎可以肯定，每笔交易都会在$j$层产生正效用，因此将包含在某个层中（应该是说所有交易都加入这一层）。只要到达率高于吞吐量$n \gt  B$，就能证明这一点。

6. 引理4：对于任意区块链$(B,d,p)$，和正则需求$n,F$，其中$p$是EIP-1559稳定的，可得：对于所有$1\leq j\lt k$ 都有$p_j\geq p_{j+1}$。
	
证明：假设对于$1\leq j\lt k$都有$p_j\lt p_{j+1}$，在这种情况下，第$j$层会有比第$j+1$层更低的价格和更低的延迟。因此，根据公式3，我们可得对于任意交易$i$而言，$u(i,j)\gt u(i,j+1)$，这使得$T_{j+1}=\emptyset$。但是，$p_{j+1}\gt p_j\geq 0$会导致矛盾，因为空层的价格一定是0。

7. 引理5：令$f:[0,1]^k\rightarrow R^k_{\geq 0}$是一个具有以下属性的函数：
	1. $f(x_1,x_2,...,x_k)$是在$x$上均匀连续的，且随$x_j,j\in[k]$递减。
	2. 如果$x_j=1$，则$f_j(x)=0$，其中$f_j$是$f(x)$的第j个坐标。
	3. 如果$x_j=0$，则$\sum_{j\in[k]}f_j(x)\gt B$
	4. 对于$y=x+\delta e_j$使得$x,y\lt 1$，可得当$l\neq j$时$f_j(y)\lt  f_j(x)$且$f_l(y)\geq f_l(x)$。
	5. 因此，对于任意满足$\sum_{j\in[k]}B_j=B$的$B_1,B_2,...,B_k\gt 0$，对于任意$j\in[k]$，都存在一些$x\in[0,1]^n$使得$f_j(x)=B_j\gt 0$。
8. 定理1：对于任意有着固定$B,d$和任意$n,F$的区块链，都存在$p$使得$(B,d,p)$是兼容且EIP-1559稳定的。
9. 引理6：给定$B,d,n,F$，和两个不同的价格向量$p\geq p'$（即每个$p$中的价格都大于等于$p'$中对应的价格），我们可得对于所有$j\in[k]$都有$\sum_{j\in[k]}T_j\leq\sum_{j\in[k]}T'_j$。

证明：令S表示一组估值，如果这些估值被包含在它们最喜欢的区块链层级$(B,d,p')$中，将会带来正效用。显然如果将它们纳入$(B,d,p)$而不是同一层级，它们的效用都会增加。类似地定义$S'$，可得$S'\subseteq S\Rightarrow Pr[S']\leq Pr[S]$。因为$S$和$S'$能根据每个交易的首选层被划分为不相交的集合$S_j$，这样$Pr[S_j]=T_j$乘以$n$就得到了所要求的结果。

10. 定理2：对于有着固定B和d以及任意$n,F$的区块链，如果存在$p$使得$(B,d,p)$是兼容且EIP-1559稳定，则$p$是独一无二的。

证明：假设存在两个不同的价格向量$p\neq p'$使得$(B,d,p)$和$(B,d,p')$都是在需求$n,F$下兼容且EIP-1559稳定。因为$p\neq p'$，我们可以把价格集合划分为两部分：$P^{+}=\{j\in[k]|p'_j\gt p_j\}$和$P^{-}=\{j\in[k]|p'_j\leq p_j\}$。

我们用$T_j$和$T'_j$表示$(B,d,p)$和$(B,d,p')$的层级划分。

假设$P^{-}=\emptyset$，我们只需要说明对于$(B,d,p')$和需求$n,F$可得
$$
	\sum_{j\in[k]}E[T'_j]\lt \sum_{j\in[k]}B_j\tag{10}
$$

就可以确定价格$p'$并非EIP-1559稳定，因为它们都是正数且一些层没满。
	
为了得到这一矛盾，我们假设等式10成立。令$\delta=min_j\{p'_j-p_j|j\in[k]\}$并定义一个新的价格向量$\hat{p}=p+\delta$。通过价格兼容性和引理6，可得：
	
$$
	\sum_{j \in[k]} B_j=\sum_{j \in[k]} \mathbb{E}\left[T_j^{\prime}\right] \geq \sum_{j \in[k]} \mathbb{E}\left[\widehat{T}_j\right] \geq \sum_{j \in[k]} \mathbb{E}\left[T_j\right]=\sum_{j \in[k]} B_j\tag{11}
$$
	
因此，如果所有价格恰好增加$\delta$，则总需求保持不变。事实上，因为效用是拟线性的，并且所有价格变化量相同，所以每一层的需求保持和$(B,d,p)$一样。此外，任何价值$v$的首选层在$(B,d,p)$和$(B,d,\hat{p})$中都是一样的。我们将证明这违反了分布假设。

令$S_j$表示F的支持子集，其中包含在价格$p$下倾向于$T_j$层的交易。
	
$$
S_j=\left\{\left(v_0, h\right) \in F \mid v_0 \cdot h\left(d_j\right)-p_1 \geq v_0 \cdot h\left(d_j^{\prime}\right)-p_j \text { for all } j^{\prime} \neq j\right\}
$$

我们可得
	
$$
\mathbb{E}\left[T_j\right]=n \cdot \operatorname{Pr}\left[S_j\right] \Rightarrow \operatorname{Pr}\left[S_j\right]=\frac{B_j}{n}
$$

令 S 为F的支持子集，其中包含将加入$p$下某个层的有着相似$\hat{S}$的交易：
	
$$
S=\left\{\left(v_0, h\right) \in F \mid v_0 \cdot h\left(d_j\right)-p_j \geq 0 \text { for some } j \in[k]\right\}
$$

当然，$S=\cup_{j\in[k]}S_j$且每个$S_j$都不相交，从公式11可以推出：
	
$$
\sum_{j \in[k]} \mathbb{E}\left[\widehat{T}_j\right]=\sum_{j \in[k]} \mathbb{E}\left[T_j\right] \Rightarrow \sum_{j \in[k]} \mathbb{E}\left[\widehat{T}_j\right] / n=\sum_{j \in[k]} \mathbb{E}\left[T_j\right] / n \Rightarrow \sum_{j \in[k]} \operatorname{Pr}\left[\widehat{S_j}\right]=\sum_{j \in[k]} \operatorname{Pr}\left[S_j\right]\tag{12}
$$

令$\mathbb{1}_{t\in S}$表示交易t加入S的指示变量。因为所有$S_j$都不相交：
	
$$
\sum_{j \in[k]} \operatorname{Pr}\left[\widehat{S_j}\right]=\sum_{j \in[k]} \operatorname{Pr}\left[S_j\right] \Rightarrow \operatorname{Pr}\left[\cup_{j \in[k]} \widehat{S_j}\right]=\operatorname{Pr}\left[\cup_{j \in[k]} S_j\right] \Rightarrow \mathbb{E}\left[\mathbb{1}_{t \in \widehat{S}}\right]=\mathbb{E}\left[\mathbb{1}_{t \in S}\right]
$$

为了简化计算，我们可以在价值的折扣函数$h$条件下使用全期望定律。把所有内容合起来可得：
$$
\sum_{j \in[k]} \mathbb{E}\left[\widehat{T}_j\right]=\sum_{j \in[k]} \mathbb{E}\left[T_j\right] \Rightarrow \mathbb{E}\left[\mathbb{E}\left[\mathbb{1}_{t \in \widehat{S}} \mid h\right]\right]=\mathbb{E}\left[\mathbb{E}\left[\mathbb{1}_{t \in S} \mid h\right]\right]\tag{13}
$$

然后得到：
	
$$
\mathbb{E}\left[\mathbb{1}_{t \in S} \mid h\right]=\operatorname{Pr}\left[v_0 \geq V_h \mid h\right] \quad \text { and } \quad \mathbb{E}\left[\mathbb{1}_{t \in \widehat{S}} \mid h\right]=\operatorname{Pr}\left[v_0 \geq \widehat{V_h} \mid h\right]
$$

这是因为以$h$为条件，价值随$v_0$增加，因此存在一个最小的$V_h$，高于该值的每个$v_0$都会加入某个层。可以看出，对于$v_0=\hat{V_h}$（被包含在$\hat{p}$下的某层$j$），可得：

$$
\widehat{V_h} \cdot h\left(d_j\right)-\left(p_j+\delta\right) \Rightarrow \widehat{V_h} \cdot h\left(d_j\right)-p_j \geq \delta\gt 0
$$

因此，存在某价值$v_h-\varepsilon$使得$\left.\widehat{V_h}-\varepsilon\right) \cdot h\left(d_j\right)-p_j\gt 0$。由此在价格$p$下，对于折扣函数$h$，任何$\hat{V_h}\geq v_0 \geq \hat{V_h}-\varepsilon$都会被包含进某层：

$$
\begin{aligned}
\operatorname{Pr}\left[v_0 \geq V_h \mid h\right] & \geq \operatorname{Pr}\left[v_0 \geq \widehat{V_h}-\varepsilon \mid h\right] \\
& \geq \operatorname{Pr}\left[\widehat{V_h}\gt v_0 \geq \widehat{V_h}-\varepsilon \mid h\right]+\operatorname{Pr}\left[v_0 \geq \widehat{V_h} \mid h\right] \\
& \gt \operatorname{Pr}\left[v_0 \geq \widehat{V_h} \mid h\right] .
\end{aligned}
$$

这意味着$E[\mathbb{1}_{t\in S}|h]\gt E[\mathbb{1}_{t\in \hat{S}}|h]$，无论采用哪边的期望价值都与公式13矛盾。

对于$P^{-}\neq \emptyset$的情况，显然，$(B,d,p')$对应的任意层$j\in P^+$的需求只会严格小于$(B,d,p)$对应的需求，随着这些层的价格上涨，而其余层价格保持不变或下降。在证明过程中，我们对之前的分析稍作修改，增加了一种情况，即$P^+$的价格并不稳定，而是有可能有一些需求溢出到 P 层，使得新的价格与需求不符。

11. 总结：这一部分说的是兼容且稳定的区块链具有的性质。
	1. 命题3很好理解，直观来说就是在其他因素不变的情况下，价格变化会引起期望需求改变，而最小和最大分别是n和0，B恰好在这个区间上，这个变化是连续的，显然中间肯定存在一个值，对应了期望效用是B的情况。
	2. 命题4也好理解，如果一个区块链是兼容且稳定的，这意味着每一层的期望需求应该恰好等于这一层的容量。如果是大于的情况，则不满足兼容的定义；如果是小于的情况，则按照稳定的性质，这一层价格应该为0，这使得还没加入的交易都会争相加入，而在本文的场景下交易数量大于区块链所有层的总容量，去掉放到其他层的交易以后，剩下的交易都想加入这一层，而这个需求必然大于这一层的容量，因此与兼容冲突了。
	3. 引理2这里，这篇论文应该是用1表示最高价，从公式可以看出如果某一层价格很高，那它的期望需求就是0。
	4. 引理3和EIP-1559稳定感觉说的是一个意思，价格为0就意味着这一层还有空，期望需求小于它的容量。
	5. 引理4也很显然，兼容且稳定时，延迟低的层价格会高。
	6. 引理5有点抽象，通俗来说，函数f()可以看作是从层级的价格p到这一层的预期需求$E(T_k)$的映射，引理5表明可以通过调整价格使得预期需求量等于这层的容量$B_k$，从而满足兼容且稳定的需求。
	7. 定理1可以从引理5引申出来。
	8. 引理6直观来看，价格上涨就会导致需求下降，很容易理解。它是为后面证明价格唯一性做支撑的。
	9. 定理2的证明比较多，这个定理本身的含义可以概括为：对于确定的区块大小和延迟，如果存在可以使区块链平稳运行的价格，那么这组价格就是唯一的。用的反证法：
		1. 假设存在两个不同的价格$p$和$p'$都能让同一个区块链兼容且稳定；
		2. 把价格可能取值的集合划分为p'高于p的$P^+$集合，以及p'低于or等于p的$P^-$集合；
		3. 如果$P^-$是空的，意味着只存在p'大于p的情况，根据引理6，如果p是兼容且稳定的，则意味着p'对应的期望需求会更小，如果p'也兼容且稳定，那价格一定是0，这使得p要小于0，而这又违背了定义，所以这种情况不存在（证明部分的公式比这个复杂）；
		4. 如果$P^-$非空，在$P^+$集合，价格从p涨到p'，会导致期望需求下降，感觉和前面一样，都是要么不兼容要么不稳定。
		5. 总的来说这个定理的意思就是，在区块大小和延迟不怎么改变的情况下，需求波动后会带来价格的波动，从而使得不改变大小和延迟的情况下还能兼容稳定。
### Steady-state of the Tiered Pricing Mechanism
1. 在负载$n,F$和固定的$B$下，分层定价机制旨在找到$(B,d,p)$，使得：
$$
d_{j+1} \geq \lambda_j \cdot d_j \text { for } \lambda_j\gt 1 \text {, and } p_{j+1} \leq \mu_j \cdot p_j \text { for } \mu_j\lt 1\tag{14}
$$
2. 定理3：对于任意B，因子$\lambda_j,\mu_j$和正则需求$n,F$，都存在一个区块链$B,d,p$既兼容和EIP-1559稳定，也满足不等式14。

证明：将$d_1$设置为0，由此定义：
$$
	d_{j+1}=\max \{\lambda_{j} \cdot d_{j}, \underset{d}{\operatorname{argmin}}\{\operatorname{Pr}\left[h(d)\lt \mu_{j} \cdot h\left(d_{j}\right) / 2\right]\gt 1-\delta \cdot \frac{1}{k-1} \cdot \frac{1}{n}\}\}
$$

本质上，$d_{j+1}$远大于$d_j$，因此几乎所有交易产生的折扣因子至少比$d_j$小$\lambda_j/2$。我们可以用以下 的集合来描述这个“几乎所有”属性：
	$$
	H_{j+1}=\{(v_{0}, h) \in \operatorname{supp}(F) \mid h(d_{j+1})\lt \mu_{j} \cdot h(d_{j})\}
	$$

且$H=\cap_{j\geq 1}^kH_j$。在$n$个交易的样本中，我们观察到没有一个交易的折扣函数位于集合H之外的概率是：	
$$
	\begin{aligned}
\operatorname{Pr}\left[\forall i \in[n] t_{i} \in H\right] & =1-\operatorname{Pr}\left[\exists i \in[n] \text { s.t. } t_{i} \in H^{C}\right] \\
& \geq 1-n \cdot \operatorname{Pr}\left[\cup_{j \geq 1}^{k} H_{j}^{C}\right] \\
& \geq 1-n \cdot \sum_{j \geq 1}^{k} \delta /(k-1) / n=1-\delta .
\end{aligned}
$$
因此，可以将延迟$d_{j+1}$设置得足够高，这样在概率至少为$1-\delta$的情况下，样本中所有交易的折扣函数都会使连续层级的价值至少降低$\mu_j$倍。
	 
对于我们刚才描述的延迟$d_1, d2, ...,d_k$，让$p$成为保证存在的兼容和 EIP-1559 稳定的价格。根据命题 4，我们知道，对于这些价格，每个层级都必须完全满员。因此，对于任何层级$j$，都必须存在一个$h\in H$ 且使得层级$j+1$优于$j$的$v_0$的交易，且其效用为正。用公式来表述：

$$
\begin{aligned}
v_{0} \cdot h\left(d_{j}\right)-p_{j} & <v_{0} \cdot h\left(d_{j+1}\right)-p_{j+1} \\
\Rightarrow v_{0} \cdot h\left(d_{j+1}\right) / \mu_{j}-p_{j} & <v_{0} \cdot h\left(d_{j+1}\right)-p_{j+1} \\
\Rightarrow v_{0} \cdot h\left(d_{j+1}\right) & <\frac{p_{j}-p_{j+1}}{1 / \mu_{j}-1} .
\end{aligned}
$$
因为效用为正（即$v_0\cdot h(d_{j+1})-p_{j+1}\gt 0$），可以进一步得到：
	$$
	\begin{aligned}
p_{j+1} & <\frac{p_{j}-p_{j+1}}{1 / \mu_{j}-1} \\
\Rightarrow p_{j+1} & <p_{j} \cdot \mu_{j} .
\end{aligned}
	$$
因此，对于适当选择的延迟，可以保证价格也将遵循我们的约束条件。

上述定理可以转换为分层定价机制执行不等式 14 所定义的政策的能力。

3. 推论1：对于任意正则需求$n,F$以及任意$k\geq B$，和任意参数集$\{(a_j,\lambda_j,\mu_j)\}_{j\in[k-1]}$，其中$a_j\in(0,1],\lambda_j\in R_{>1},\mu_j\in(0,1)$，对于$j\in[k-1]$且$\sum_j a_i=1$，有着相同参数和$tFreq:=\infty$的分层定价机制执行策略$P:=\{(a_i,d_i,p_i)\}_{i\in[k]}$，其子句满足不等式14.
4. 遗憾的是，有很多 p 和 d 对都能满足这些特性。例如，最后一级的延迟总是可以增加（降低相应的价格），同时保持我们的保证。然而，我们的分层定价机制能找到局部最小解，不会不必要地增加延迟。
5. 定理4：对于任何$B$、因子$\lambda_j,\mu_j$和需求$n,F$，都存在一个兼容、EIP-1559 稳定且满足不等式 14 的区块链$(B,d,p)$。此外，在不牺牲任何保证的情况下，任何延迟都可以被独立降低。