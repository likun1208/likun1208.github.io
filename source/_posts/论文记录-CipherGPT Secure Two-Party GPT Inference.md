---
title: "论文记录-CipherGPT: Secure Two-Party GPT Inference"
date: 2023-12-08 15:22:40
tags:
  - LLM
  - Privacy
  - Security
  - MPC
categories: 论文
description: LLM和MPC结合的论文之一
---
# CipherGPT: Secure Two-Party GPT Inference

## Abstract
1. GPT面临隐私问题；
2. 由于数据量大、激活函数复杂，现有的安全方案不支持GPT；
3. 本文：CipherGPT——第一个安全两方GPT框架
	1. 提出了一种专为 GPT 推理定制的安全矩阵乘法，与 SOTA 相比，速度提高了 2.5 倍，带宽减少了 11.2 倍
	2. 提出了一种用于安全计算 GELU 的新协议，在运行时间、通信量和精度方面分别比 SOTA 提高了 4.2 倍、3.4 倍和 10.9 倍
	3. 提出了首个 top-k 采样协议

## Intro
1. 介绍ChatGPT及其面临的隐私问题
2. 安全推理：一种两方加密协议，运行推理阶段的方式如下：
	1. 服务器 (S) 对客户端的输入一无所知，而客户端 (C) 除了推理结果之外对模型一无所知。
	2. 协议通过让 S 和 C 运用同态加密和秘密共享等定制加密技术在加密输入上运行加密模型来进行。
	3. 通常引入预处理阶段来准备一些昂贵且与输入无关的工作，以便在线阶段可以高效地完成。
3. 现有安全推理应用到GPT的问题：
	1. 一些技术只针对某种网络结构，而不支持GPT；
	2. LLM需要大量高维矩阵乘法和复杂的数学函数（例如 GELU）。
4. 本文贡献：CipherGPT
	1. 基于 VOLE 的矩阵乘法
	2. 基于样条的GELU
	3. 基于洗牌的前K项选择
	4. 安全采样

## 预备知识
### 安全推理和威胁模型
安全推理是一种两方加密协议，有助于客户端 C 和服务器 S 之间的模型推理。该协议确保 C 只获得有关模型架构和推理结果的知识，同时隐藏 S 模型的所有其他细节。类似地，S 仍然不知道 C 的输入以及推理的输出。在 GPT 推理的上下文中，C 的输入是提示，S 的模型包含 Transformer 解码器的多次迭代以及 vec2word 层。

我们假设 C 或 S 可以是半诚实对手，它遵循协议规范，但尝试在协议执行期间收集尽可能多的信息。我们假设对手在计算上是有限的，并且使用$\lambda$来表示计算安全参数。

| 符号 | 含义 |
|:---:|:---|
|$C$|客户端|
|$S$|服务器|
|$n$|每层输入向量的长度|
|$L$|初始输入左移位|
|$l$|输入左移后的位长|
|${\langle x\rangle}^l$|$(\langle x \rangle_S^l,\langle x \rangle_C^l)$使得$x=\langle x \rangle_S^l+\langle x \rangle_C^l$ mod $2^l$|
|$F_{Mult}$|密钥共享乘法的理想功能：将$g$位整数与$h$位整数相乘得到$l=(g+h)$位输出，且无溢出|
|$F_{CMP}$|比较$b\leftarrow CMP(x,y)$的理想功能：如果$x\geq y$，则$b=1$，反之则$b=0$|
|$F_{MUX}$|多路复用器$y\leftarrow MUX(x,b)$的理想功能：如果$b=1$，则$y=x$，如果$b=0$，则$y=0$|
|$F_{Trunc}$|截断$y\leftarrow Trunc(x,s)$的理想功能：对于$x,y\in\mathbb{Z}_{2^l}$，$y=x\gg s$|
|$F_{TR}$|截断后减少$y\leftarrow TR(x,s)$的理想功能：对于$x\in\mathbb{Z}_{2^l}$和$y\in \mathbb{Z}_{2^{l-s}}$，$y=x\gg s$|
|$F_{LUT}$|查找表$T[i]\leftarrow LUT(T,i)$的理想功能|
|$F_{Shuffle}$|洗牌的理想功能|
|$\alpha$|$y:=GELU(x)$的分割：当$x<-\alpha$时$y:=0$；当$-\alpha\leq x \leq \alpha$时$y:=GELU(x)$；当$x>\alpha$时$y:=x$|
|$s$|$2^s$是在$[\\alpha,\alpha]$内的间隔数的数量|
|$(a_i,d_i)$|$y=a_ix+d_i$是每个间隔内逼近$GELU(x)$的线性函数|
|$g$|$a_i$的位长度|
|$t$|响应词（输入矩阵）|
|$N$|FHE中的多项式模度|
|$M$|注意力头|
|$T$|温度|

### 密码学原语
1. 秘密共享：我们在不同的2次幂环上使用2-2附加秘密共享方案。对于$x\in\mathbb{Z}_{2^t}$，我们用$\langle x \rangle^l=(\langle x \rangle^l_S,\langle x \rangle^l_C)s.t.x=\langle x \rangle^l_S+\langle x \rangle^l_C mod 2^l$表示其shares。为了简化表达，当上下文无关时我们省略上标的$l$。
2. 非均匀位宽乘法：理想功能$F_{Mult}$以$\langle x \rangle^g$和$\langle y \rangle^h$为输入，返回$\langle z \rangle^l$，其中$z=xy$且$l=g+h$。一个实现该功能的简单方法是首先将输入都扩展到$l$位，然后使用标准协议进行统一位宽的秘密共享乘法。SIRNN提供了一个协议，其性能比这个简单的解决方啊高1.5倍。该协议的通信复杂度是$\mu(\lambda+\mu/2+1/2)+mn$，其中$\mu=min(m,n)$。
3. 安全比较：理想功能$F_{CMP}$以$\langle x \rangle^l$和$\langle y \rangle^l$为输入，返回$\langle b \rangle^l$，其中若$x\geq y$则$b=1$，否则$b=0$。$CrypTFlow2$为$F_{CMP}$提供了一个有效的协议，其通信成本小于$\lambda l+14l$位和$\log l$轮。
4. 安全多路复用器：理想功能$F_{MUX}$以$\langle x \rangle^l$和$\langle b \rangle^l$为输入，返回$\langle y \rangle^l$，其中如果$b=1$则$y=x$，如果$b=0$则$y=0$。本文采用的安全多路复用器是SIRNN提出的，通信要求$2(\lambda+l)$位。
5. 安全截断：理想功能$F_{Trunc}$以$\langle x \rangle^l$和$s$为输入，返回$\langle y \rangle^l$，其中$y=x\gg s$。SIRNN提供了安全截断的协议，通信要求小于$\lambda(l+3)+15l+s+20$位和$\log l+3$轮。
6. 截断后减小：理想功能$F_{TR}$以$\langle x \rangle^l$和$s$为输入，返回$\langle y \rangle^{l-s}$，其中$y=x\gg s$。需要注意，这个和前一个的区别在于这个会通过右移操作将输出减少到一个更小的环上，而前一个则将输出保留在原始环上。SIRNN提供了协议，通信成本小于$\lambda(s+1)+l+13s$位。
7. 查表：理想功能$F_{LUT}$以$\langle i \rangle$为输入，返回$\langle T[i] \rangle$，其中$T$是有M个条目的表。该函数通过对$\tbinom{M}{1}-OT$的单次调用来实现。更有效的解决方案是先将LUT描述转换为布尔表达式，然后使用多扇入内积对其进行评估。该协议在预处理阶段需要$(mt+4)(2^M-M-1)$位通信，在在线阶段需要$2\sigma$位。
8. 秘密共享洗牌：理想功能$F_{Shuffle}$以$\langle \mathbf{x} \rangle$和$\langle\pi\rangle$为输入，返回$\langle\pi(\mathbf{x})\rangle$，其中$\pi$是排列函数。Chase等提出了使用OT和PRG等轻量级原语来实现该功能的高效构造。该方法涉及使用PRF来构建排列共享协议，该协议允许两方使用一方选择的排列来排列输入向量。这个排列和共享协议运行两次，每方各选一次。为了混洗$n$个$l$位元素，该协议的通信成本与$\lambda n\log n+nl\log n/\log T$成正比，计算成本位$(nT\log n/\log T)(l/\lambda)$对称密钥操作，其中$T$在16和256之间。
9. 子域向量遗忘线性评估：VOLE是一个两方功能，它从发送方获取标量$x\in\mathbb{F}_p$并生成 VOLE 相关性：
$$
w = ux + v \tag{1}
$$
接收方学习$(\mathbf{u},\mathbf{v})\in_R \mathbb{F}_p^n\times\mathbb{F}_p^n$，发送方学习$w\in_R\mathbb{F}_p^n$。VOLE 通常基于带噪声奇偶校验（LPN）和可标点 PRFs 来构建，因此其复杂性几乎与$n$无关。子域VOLE(sVOLE)是VOLE的广义化，其中$\mathbf{u}\in_R\mathbb{F}_p^n,x\in\mathbb{F}_q,\mathbf{w,v}\in_R\mathbb{F}_q^n,q=p^m$。sVOLE 实现的任务与运行 m 个普通 VOLE 实例相同，但成本更低。VOLE 和 sVOLE 最初是针对有限域提出的。Baum 等人提出了在有限环（如$\mathbb{Z}_{2^l}$）上运行的方法。
10. 同态加密：全同态加密（FHE）是一种允许对加密数据进行任意操作的加密方案 。在实践中，它通常以分级的方式使用：操作只能在有限的时间内进行，否则密文无法解密。在大多数 FHE 密码系统中，明文被编码为来自商环$\mathbb{Z}_p[x]/(x^N +1)$的多项式，其中$N$是 2 的幂次，$p$是明文的模数。然后将明文多项式加密为密文多项式$\mathbb{Z}_q[x]/(x^N+1)$，其中$q$是密文模数，它决定了密文的安全级别以及可执行操作的次数。

## 安全矩阵乘法
MatrixMul运算分别从C和S获取两个矩阵$\mathbf{X}\in\mathbb{Z}_{2^l}^{n\times m}$和$\mathbf{Y}\in\mathbb{Z}_{2^l}^{m\times k}$作为输入，输出$\langle \mathbf{Z}\rangle$，其中$\mathbf{Z}=\mathbf{XY}\in\mathbb{Z}_{2^l}^{n\times k}$。大多数已有方案用同态乘法和加法来实现上述共识的隐私计算。SIMD 技术通常用于通过将 N 个元素批处理为单个 RLWE 密文来摊销成本，但它需要昂贵的同态旋转来求和。Cheetah用系数填充代替 SIMD，以消除昂贵的旋转。尽管如此，仍然需要传输$\geq \frac{2n\sqrt{mk}}{\sqrt{N}}$ RLWE密文，并执行$\geq \frac{nmk}{N}$密文-明文同态乘法。

回想一下，GPT 需要自回归生成响应词。因此，单个 GPT 推理需要对具有相同Y的不同X运行 MatrixMul。我们的目标是通过利用 GPT 的这一特性来降低 MatrixMul 的摊销成本。

$\mathbf{X}=\[\mathbf{x}_1,\mathbf{x}_2,...,\mathbf{x}_m\](\mathbf{x}_i\in\mathbb{Z}_{2^l}^n$ 是 $\mathbf{X}$ 的每一列)，$\mathbf{Y}^T=\[\mathbf{y}'_1,\mathbf{y}'_2,...,\mathbf{y}'_m\](\mathbf{y}'_i\in\mathbb{Z}_{2^l}^k$是$\mathbf{Y}$的每一行)，则$\mathbf{Z}=\sum_{i=1}^m(\mathbf{x}_i\otimes\mathbf{y}'_i)$。假设S和C需要生成t个响应词，即有t个输入矩阵：
$$
\begin{align}
\mathbf{X}_1=&[\mathbf{x}_{1,1},\mathbf{x}_{1,2},...,\mathbf{x}_{1,m}],\\
\mathbf{X}_2=&[\mathbf{x}_{2,1},\mathbf{x}_{2,2},...,\mathbf{x}_{2,m}],\\
&... ... \\
\mathbf{X}_t=&[\mathbf{x}_{t,1},\mathbf{x}_{t,2},...,\mathbf{x}_{t,m}].
\end{align}
$$
令$\mathbf{x}'_i=\mathbf{x}_{1,i}\|\mathbf{x}_{2,i}\|...\|\mathbf{x}_{t,i},\forall i\in[1,m]$，则
$\mathbf{x}'_i\otimes\mathbf{y}'_i=(\mathbf{x}_{1,i}\otimes\mathbf{y}'_i)\|(\mathbf{x}_{2,i}\otimes\mathbf{y}'_i)\|...\|(\mathbf{x}_{t,i}\otimes\mathbf{y}'_i)$.

可得
$$
\sum_{i=1}^m(\mathbf{x}'_{i}\otimes\mathbf{y}'_i)=\mathbf{Z}_1\|\mathbf{Z}_2\|...\|\mathbf{Z}_t.
$$

因此，我们可以通过m个外积来计算t次矩阵乘法。

给定已经提前知道的$\mathbf{Y}$，我们可以引入一个预处理阶段，让S和C生成m个sVOLE相关性：
$$
\mathbf{W_i}=\mathbf{u_i}\otimes\mathbf{y}'_i+V_i,\forall i\in[1,m].
$$

其中C持有$\mathbf{u}_i\in\mathbb{Z}_{2^l}^{t\cdot n}$，是一个长为$t\cdot n$的向量，和$\mathbf{V}_i\in\mathbb{Z}_{2^l}^{t\cdot n}\times k$。S持有$\mathbf{y}'_i\in\mathbb{Z}_{2^l}^{k}$，且$\mathbf{W}_i\in\mathbb{Z}_{2^l}^(t\cdot n)\times k$。

在在现阶段，对于一个输入矩阵$\mathbf{X}_j=\[\mathbf{x}_{j,1},\mathbf{x}_{j,2},...,\mathbf{x}_{j,m}\]$，C将$\langle\mathbf{x_{j,i}}_S:=\mathbf{x}_{j,i}-\mathbf{u}_i[(j-1)n+1,...,j\cdot n]\forall i\in[1,m]\rangle$发送给S，然后计算：
$$
\begin{align}
\langle\mathbf{x}_{j,i}\rangle_S\otimes \mathbf{y}'_i&=(\mathbf{x}_{j,i}-\mathbf{u}_i[(j-1)n+1,...,j\cdot n])\otimes \mathbf{y}'_i\\
&=\mathbf{x}_{j,i}\otimes\mathbf{y}'_i-\mathbf{u}_i[(j-1)n+1,...,j\cdot n]\otimes \mathbf{y}'_i
\end{align}
$$

然后可得：
$$
\begin{aligned}
\mathbf{x}_{j, i} \otimes \mathbf{y}_i^{\prime}= & \left\langle\mathbf{x}_{j, i}\right\rangle_{\mathrm{S}} \otimes \mathbf{y}_i^{\prime}+\mathbf{u}_i[(j-1) n+1, \cdots, j \cdot n] \otimes \mathbf{y}_i^{\prime} \\
= & \left\langle\mathbf{x}_{j, i}\right\rangle_{\mathrm{S}} \otimes \mathbf{y}_i^{\prime}+\mathbf{W}_i[(j-1) k n+1, \cdots, j \cdot k \cdot n] \\
& -\mathbf{V}_i[(j-1) k n+1, \cdots, j \cdot k \cdot n]
\end{aligned}
$$
注意S持有：
$$
\left\langle\mathbf{x}_{j, i}\right\rangle_{\mathrm{S}} \otimes \mathbf{y}_i^{\prime}+\mathbf{W}_i[(j-1) k n+1, \cdots, j \cdot k \cdot n]
$$
C持有：
$$
\mathbf{V}_i[(j-1) k n+1, \cdots, j \cdot k \cdot n]
$$
这意味着S和C秘密共享$\mathbf{x}_{j,i}\otimes\mathbf{y}'_i$，因此它们可以本地计算$\mathbf{Z}_{j}=\sum_{i=1}^m\left(\mathbf{x}_{j, i} \otimes \mathbf{y}_i^{\prime}\right)$的秘密共享。它们可以由此计算所有Z。

对比了本文矩阵乘法和已有的两个矩阵乘法的成本，说明本文方法的成本与n无关，因此n较大时本方法更能节省成本。

## 安全GELU
### 直觉
GELU的曲线：当$x$值较小时，它从零开始，当$x$约为$-\alpha$时，它开始偏离零。随着$x$进一步增加，GELU(x)逐渐逼近线性函数$y=x$。由此可以将曲线划分为以下三部分：
- 当$x\lt -\alpha$时$y=0$；
- 当$-\alpha\leq x\leq \alpha$时$y=GELU(x)$；
- 当$x\gt -\alpha$时$y=x$。

第1和第3部分很容易计算，在第二部分，我们用多项式样条来近似GELU曲线。如图所示，我们将这个区间划分为几个小区间，并使用线性函数$y=ax+d$来逼近小区间内的曲线。我们参考[30]中的第 5.3.2 节了解查找线性函数的详细过程。值得注意的是，这种近似不需要对模型的训练阶段进行任何修改。

![GELU曲线图](https://github.com/likun1208/image/blob/master/Cipher-1.png?raw=true)

我们可以使用$LUT$找到x所在的小区间，并以秘密共享的方式计算相应的线性函数。然而，对于$[−\alpha,\alpha]$，我们必须先确定$x$的正负性，然后分别在区间$[−\alpha, 0]$和$[0,\alpha]$中查找。为了避免这种情况，我们将整个曲线右移$\alpha$，如图 1（右）所示，之后第二个区间变为$[0, 2\alpha]$，从而允许我们执行单次查找。

### 详细说明
算法1详细说明了我们如何实现安全计算$y:= GELU(x)$：

![GELU算法](https://github.com/likun1208/image/blob/master/Cipher-2.png?raw=true)

请注意，模型的初始输入已左移$L$位，这反过来又影响$x$的值，导致$x$也左移$L$位。为了保持所需的对齐，我们将$\alpha$放大$2L$倍（第 1 行）。那么，分割值就变成$\alpha':=2^L\alpha$。

曲线右移还需要考虑比例因子。也就是说，我们不应该直接将曲线右移$\alpha$，而应该将其右移$\alpha'$。同样，为了确保正确对齐，GELU 的输入应调整为$x':=x\alpha'$，这可以通过将$\alpha'$添加到$x$的任何份额来实现（第 2 行）。

处理小区间：令$\beta:=2\alpha'$，则第二个大区间可转换为$[0,\beta]$。假设$x'\in[0,\beta]$，我们只需要考虑$x'$的较低$h:=\log \beta$位。此，我们让S和C提取$\langle x'\rangle^l$的低$h$位并得到$\langle x'\rangle^h$（第5行），这可以在本地完成，无需任何通信。

假设$[0,\beta]$被分成$2s$个小区间，那么我们可以通过检查它的高$s$位来找到$\langle x' \rangle^h$的区间。为此我们让S和C在$\langle x'\rangle^h$上运行截断后减小协议（第6行），由此可以得到$\langle i\rangle^s$，其中$i\in\mathbb{Z}_{2^n}$表示$x'$所属的小区间索引。

S持有表T，其中每个条目存储对应于各自小区间的线性函数的系数，获得$i\in\mathbb{Z}_{2^n}$后，S和C执行LUT从而通过$(\langle a_i\rangle^g,\langle d_i\rangle^l)$的秘密共享得到$T$的第$i$个条目（第7行）。然后在$\langle a_i\rangle^g$和$\langle x'\rangle^h$上运行非均匀位宽乘法（第8行），得到$\langle ax\rangle^l,l=g+h$。将$\langle d_i\rangle^l$和$\langle ax\rangle^l$相加可得$\langle z\rangle^l$，这很可能就是GELU(x)的结果。

处理大区间：上述处理小区间的过程仅在$x'\in[0,\beta]$时有效，事实上，当$x'\notin[0,\beta]$时，第5行的截断会导致$x'$信息丢失。因此，我们使用多路复用器来确保当$x'\notin[0,\beta]$时不会返回$\langle z\rangle^l$。

S和C首先安全比较$x'$和$beta$的大小并得到$b$（第10行），若$x'\geq\beta$则$b=1$，否则$b=0$。然后他们安全比较$x'$和0的大小并得到$b'$（第11行），若$x'\geq 0$则$b'=1$，否则$b'=0$。注意，$b, b'$的组合只有以下三种可能性：
- $b=1,b'=1$
- $b=0,b'=1$
- $b=0,b'=0$

第二种情况$b\oplus b'=1$表示$x'\in[0,\beta]$，而另外两种情况$b\oplus b'=0$表示$x\notin[0,\beta]$。因此，我们可以用$b\oplus b'$作为控制信号来实现$z$的多路复用器。具体来说，S和C将$\langle z\rangle^l$和$\langle b\rangle^l\oplus \langle b'\rangle^l$输入到多路复用器，得到$\langle u\rangle^l$（第12行），如果$b\oplus b'=1$则$u=z$，否则$u=0$。

然后，S和C将$\langle x\rangle^l$和$\langle b\rangle^l$输入给另一个多路复用器得到$\langle v\rangle^l$（第13行），如果$b=1$则$v=x$，否则$v=0$。该多路复用器判断$x'\lt\beta$是否成立，如果成立则$v=x'$。$GELU(x)$最终结果是$\langle y\rangle^l:=\langle u\rangle^l+\langle v\rangle^l$。注意这里并不需要额外的多路复用器来处理$x'<0$的情况，因为此时$y=0$。

SIRNN [36]、Iron [25] 和我们的安全 GELU 解决方案之间的加密操作数量。显然，我们的解决方案更加轻量级。此外，我们的解决方案在精度上也更好：SIRNN 和 Iron 中的多步过程分别涉及求幂和倒数近似，在每一步都会引入精度误差；这些误差在整个过程中不断累积，导致整体误差很大，而我们的单步方法不会出现这种情况。我们的实验结果验证了这个猜想。

## 安全前K项选取
在 vec2word 层，GPT 模型生成一个包含所有可能单词概率的向量。需要从该向量中选出概率最大的前$K$个，并根据所选概率对最终响应词进行采样。本节将重点讨论从长度为$n$的向量中选出$K$个最大概率值的过程。

算法 2 提供了我们的 TopK 协议的详细描述。在高层，输入元素首先被安全地打乱（第 1 行）；然后采用基于比较的选择来从打乱的列表中识别前 K 个元素（第 2 行）。

算法2中的选择函数以递归方式运行。在每次递归中，选择向量的最后一个元素作为主元（第 5 行）；向量被划分为两部分：小于主元的元素，表示为$S_L$，以及大于或等于主元的元素，表示为$S_R$（第 6-15 行）。为了分割向量，将其所有元素与主元进行比较（第 8 行）。可以在不损害原始元素隐私的情况下揭示比较结果（第 9 行）。这是因为原始元素已被打乱，因此比较结果与实际值无关。

如果$S_R$的大小（用$K'$表示）正好是$K$，则意味着$S_R$中的所有元素都是我们要选择的前$K$个最大元素（第19行）。如果$K'>K$，则对$S_R$执行下一次递归以进一步缩小选择范围（第 21 行）。另一方面，如果$K'\lt K$，则执行下一次递归以从$S_L$中选择前 ($K-K'$) 个元素，然后将其与$S_R$组合以获得前$K$个元素的最终集合（第 21 行）。

值得一提的是，只有 CMP（第 8 行）需要 S 和 C 之间的交互；该算法的其余步骤可以由各方在本地执行，无需任何交互。选择函数需要$\mathcal{O}(n) CMP$。

## 安全采样
在本节中，我们将详细解释我们的安全采样协议。它以$K$个秘密共享概率$(p_1,...,p_K)$作为输入，其中每个概率通过乘以$2^L$并去掉小数部分而缩放为整数$x_i$。协议的输出是秘密共享索引$j$：
$$
\operatorname{Pr}(j=i)=x_i / \sum_{k=1}^K x_k
$$
我们将在第 7.5 节中解释如何将此索引映射到响应词。

算法3提供了安全采样协议的详细描述。其基于的观察是，对于随机$p'\in [0,1]$，所选索引$j$满足：
$$
\sum_{k=1}^{j-1} p_k \leq p^{\prime}<\sum_{k=1}^j p_k .
$$

由于$(p_1,...,p_K)$已经按$2^L$缩放，$p'$应该也跟着缩放。为此，我们让C在$[0,2^L-1]$采样一个整数$v$（第1行），S和C安全比较$v$和每个$\sum_{k=1}^ix_k,\forall i\in[1,K]$（第2-6行），由此得到一个秘密共享的位向量$\langle\mathbf{b}\rangle$，满足：
$$
b_i=1 \forall 1 \leq i<j \text { and } b_i=0 \forall j \leq i \leq K \text {. }
$$
我们的下一步是构建另一个秘密共享位向量$\langle\mathbf{b}'\rangle$，它满足：
$$
b_i^{\prime}=0 \forall i \neq j \text { and } b_j^{\prime}=1
$$
这一步可以通过在$\langle\mathbf{b}\rangle$的每一对相邻位上执行异或操作来实现（第7-10行）。那么，所需的索引为：$\langle j \rangle:=\sum_{i=1}^K F_{MUX}(i,\langle b'_i\rangle)$（第11行）。

我们注意到 v 由 C 单独采样是可以接受的，因为最终输出 j 对 C 来说仍然未知。


## CipherGPT框架

## 实验评估