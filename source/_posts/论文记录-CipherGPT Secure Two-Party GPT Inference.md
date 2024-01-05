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

![TopK算法](https://github.com/likun1208/image/blob/master/Cipher-3.png?raw=true)

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

![Sampling算法](https://github.com/likun1208/image/blob/master/Cipher-4.png?raw=true)

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

我们注意到$v$由$C$单独采样是可以接受的，因为最终输出 j 对 C 来说仍然未知。

## CipherGPT框架
CiperGPT架构和流程：它需要一个单词序列，将它们编码成单词嵌入，然后将它们传递给转换器解码器的多次迭代2。每次迭代都涉及一个自注意力层和一个前馈神经网络。 Transformer 解码器的输出被馈送到 vec2word 层，该层生成预测的响应词。
![框架图](https://github.com/likun1208/image/blob/master/Cipher-5.png?raw=true)
### 嵌入
它首先将每个输入单词映射到长度为 m 的数值向量，称为单词嵌入，这是通过在嵌入矩阵中定位相应行来实现的。接下来，每个单词嵌入都会增加一个位置嵌入，该位置嵌入由单词在输入序列中的位置确定。位置嵌入是预定义的，并按元素添加到词嵌入中。我们使用加法同态加密（AHE）来完成词嵌入和位置嵌入：

1. S使用AHE来加密嵌入矩阵的每一行，并将所有得到的密文传输到 C。实际上，我们通过将整行表示为RLWE密文的多项式系数来加密整行。请注意，词嵌入是浮点数；S 通过将它们左移 L 位并删除小数部分来将它们缩放为整数。
2. C根据输入的单词定位对应的密文，为每个密文添加一个随机数$r_i:E(w_1+r_1),····,E(w_n+r_n);$并将它们返回给S。
3. S解密密文得到$w_1+r_1,...,w_n+r_n;$添加位置嵌入$w_1+r_1+p_1,...,w_n+r_n+p_n$。
4. 由此，每个嵌入都是秘密共享的，$\langle x_i\rangle_C=-r_i,\langle x_i\rangle_S=w_i+r_i+p_i$。

我们注意到，步骤 1 只需执行一次，并且可以无限期地使用，除非嵌入矩阵发生变化。

### 层归一
嵌入编码后，$n$个输入词称为一个秘密共享矩阵$\langle \mathbf{X}\rangle, \mathbf{X}\in\mathbb{Z}_{2^l}^{n\times m}$。然后需要对每行$\mathbf{x}\in\mathbb{Z}_{2^l}^{n\times m}$执行层归一化操作。具体来说$\mathbf{x}$中的每个元素$x_i$如下归一：
$$
x_i:=\frac{x_i-\mathrm{E}[\mathbf{x}]}{\sqrt{\operatorname{Var}[\mathrm{x}]+c}} \cdot \gamma+\beta
$$
其中$E[\mathbf{x}]=\frac{1}{n}\sum x_i, Var[\mathbf{x}]=\frac{1}{n-1}\sum(x_i-E[\mathbf{x}])^2$，$\gamma,\beta$是学习参数，$\epsilon$是避免除数为0的小值。为了安全计算层归一化，我们让S和C运行如下：
1. 运行$F_{Mult}$计算每个$var_i:=(x_i-E[\mathbf{x}])^2$
2. 运行$F_{LUT}$计算$\frac{1}{\sqrt{Var[\mathbf{x}]+\epsilon}}$
3. 运行$F_{Mult}$计算$\frac{x_i-E[\mathbf{x}]}{\sqrt{Var[\mathbf{x}]+\epsilon}}$
4. 运行$F_{Mult}$计算$\frac{x_i-E[\mathbf{x}]}{\sqrt{Var[\mathbf{x}]+\epsilon}}\cdot\gamma$
5. 运行$F_{TR}$将比例减小到L位并将宽度截断为$l$位

原则上，S和C需要使用“均匀位宽乘法”来乘以两个秘密共享值，然后运行安全截断（对于每个乘法）以将规模保持在L位。然而，为了减少精度损失，我们让S和C使用“非均匀位宽乘法”（$F_{Mult}$）进行乘法，并且在LayerNorm计算后仅运行一次“truncatethen-reduce”（$F_{TR}$）。

### 掩码自注意力
自注意力是一种通过关联序列中不同位置来计算序列表示的机制。计算自注意力的第一步是创建三个矩阵：查询矩阵Q、键矩阵K和值矩阵V，通过将归一化嵌入$X\in\mathbb{Z}_{2^l}^{n\times m}$与三个训练过程中获取的矩阵（$W_Q\in\mathbb{Z}_{2^l}^{m\times m}, W_K\in\mathbb{Z}_{2^l}^{m\times m}, W_V\in\mathbb{Z}_{2^l}^{m\times m}$）相乘而得到：
$$
\begin{aligned}
& \langle\mathbf{Q}\rangle:=\langle\mathbf{X}\rangle\left\langle\mathbf{W}_Q\right\rangle \\
& \langle\mathbf{K}\rangle:=\langle\mathbf{X}\rangle\left\langle\mathbf{W}_K\right\rangle \\
& \langle\mathbf{V}\rangle:=\langle\mathbf{X}\rangle\left\langle\mathbf{W}_V\right\rangle
\end{aligned}
$$

由于$W_Q,W_K,W_V$事先已知，因此可以通过第 3 节中描述的基于 sVOLE 的解决方案来计算此类 MatrixMul。在 MatrixMul 之后，S 和 C 需要运行$F_Trunc$以确保缩放保持在 L 位。为了简单起见，我们在本节的其余部分中省略了提及的截断。

多头注意力：每个$\langle Q\rangle,\langle K\rangle,\langle V\rangle$都会随后被划分为M段，称为多头注意力，其中M表示注意力头的数量。令$m'=\frac{m}{M}$，可得：
$$
\begin{aligned}
&
\left\langle\mathbf{q}_1\right\rangle\|\cdots\|\left\langle\mathbf{q}_M\right\rangle=\langle\mathbf{Q}\rangle \text {, with each } \mathbf{q}_i \in \mathbb{Z}_{2^l}^{n \times m^{\prime}} \\
& \left\langle\mathbf{k}_1\right\rangle\|\cdots\|\left\langle\mathbf{k}_M\right\rangle=\langle\mathbf{K}\rangle, \text { with each } \mathbf{k}_i \in \mathbb{Z}_{2^l}^{n \times m^{\prime}} \\
& \left\langle\mathbf{v}_1\right\rangle\|\cdots\|\left\langle\mathbf{v}_M\right\rangle=\langle\mathbf{V}\rangle, \text { with each } \mathbf{v}_i \in \mathbb{Z}_{2^l}^{n \times m^{\prime}}
\end{aligned}
$$
通过查询矩阵和关键矩阵的乘积来计算得分矩阵：

$$
\left\langle\mathbf{s}_i\right\rangle:=\left\langle\mathbf{q}_i\right\rangle\left\langle\mathbf{k}_i^T\right\rangle \forall i \in[M]
$$
$s_i\in\mathbb{Z}_{2^l}^{n\times n}$中的每个分数决定了在对当前单词进行编码时对其他单词的关注程度。在这种情况下，如果事先不知道 qi 和 ki，则无法应用我们基于 sVOLE 的 MatrixMul。相反，我们采用[25]中提出的基于 AHE 的 MatrixMul。

自注意力掩码。在多头注意力之后，应用自注意力掩码来将每个$s_i$的上三角形清零。因此，左边的每个单词的注意力分数都比右边的单词高得多，因此模型在实践中只关注前面的单词。此步骤可以由 S 和 C 在本地完成，无需任何交互。

Softmax。将softmax运算逐行应用于每个$\langle s_i\rangle$，确保分数在该行内标准化，所有值均为正数且总和为1。为了安全计算softmax，我们优化了[25]中的方法，如下：
1. 给定一行$\mathbf{x}\in\mathbb{Z}_{2^l}^n$作为输入，我们首先标准化每个$x_i:x'_i=x_i-max(\mathbf{x})$，然后得到一个负值向量。
2. 我们通过仅考虑区间$[−16, 0]$来优化负值的指数协议。即，我们使用 $F_{CMP}$ 将$x'_i$与$−16\times 2^l$进行比较，如果$x'_i\lt -16\times 2^l$，使用$F_{MUX}$将$e^{x'_i}$的结果设置为 0。

原始指数协议运行具有$2^l$个条目的$F_{LUT}$，而我们只需要具有$2^{L+4}$个条目的$F_{LUT}$。

输出。在 self-attention 的最后一步中，softmaxed分数用于对值矩阵中的值进行加权：
$$
\left\langle\mathbf{z}_i\right\rangle:=\left\langle\mathbf{s}_i\right\rangle\left\langle\mathbf{v}_i\right\rangle \quad \forall i \in[M]
$$

这再次由基于 AHE 的 MatrixMul 完成[25]。然后，所有z被重新组合在一起：
$$
\langle\mathbf{Z}\rangle:=\left\langle\mathbf{z}_1\right\rangle\|\cdots\|\left\langle\mathbf{z}_n\right\rangle .
$$
自注意力的输出是：
$$
\langle\mathbf{X}\rangle:=\langle\mathbf{X}\rangle+\langle\mathbf{Z}\rangle .
$$

### 前馈
自注意力的输出经过LayerNorm操作。然后将所得归一化值输入前馈神经网络，该网络由两个全连接 (FC) 层和一个激活层组成。

第一个FC层计算如下：
$$
\left\langle\mathbf{X}_1\right\rangle:=\langle\mathbf{X}\rangle\left\langle\mathbf{W}_1\right\rangle+\mathbf{B}_1
$$
其中$\mathbf{X} \in \mathbb{Z}_{2^l}^{n \times m}, \mathbf{W}_1 \in \mathbb{Z}_{2^l}^{m \times k}, \mathbf{B}_1 \in \mathbb{Z}_{2^l}^{n \times k}, \mathbf{X}_1 \in \mathbb{Z}_{2^l}^{n \times k}$。然后对$\mathbf{X}_1$的每个元素应用GELU算法得到$\mathbf{X}'_1$。第二个FC层计算如下：
$$
\left\langle\mathbf{X}_2\right\rangle:=\left\langle\mathbf{X}_1^{\prime}\right\rangle\left\langle\mathbf{W}_2\right\rangle+\mathbf{B}_2,
$$
其中$\mathbf{X}'_1 \in \mathbb{Z}_{2^l}^{n \times k}, \mathbf{W}_2 \in \mathbb{Z}_{2^l}^{k \times m}, \mathbf{B}_2 \in \mathbb{Z}_{2^l}^{n \times m}, \mathbf{X}_1 \in \mathbb{Z}_{2^l}^{n \times m}$。请注意，$W_1$和$W_2$是预先已知的，因此我们基于 sVOLE 的 MatrixMul（参见第 3 节）可以应用于两个 FC 层。

输出将再次经历自注意力和前馈的多次迭代，每次迭代采用不同的权重，同时保留相同的结构。

### 向量转词
经过多次自我关注和前馈迭代后，所得输出将通过 vec2word 层以生成预测的响应词。 vec2word 中的初始操作涉及 MatrixMul 来为所有可能的单词生成 one-hot 编码：
$$
\langle\mathbf{y}_0\rangle:=\langle\mathbf{x}\rangle\langle\mathbf{W}\rangle
$$
其中$\mathbf{W}\in\mathbb{Z}_{2^l}^{m\times k},\mathbf{y}_0\in\mathbb{Z}_{2^l}^{k},\mathbf{x}\in\mathbb{Z}_{2^l}^{m}$是$\mathbf{X}\in\mathbb{Z}_{2^l}^{n\times m}$的最后一行（由于 GPT 采用的推理时间优化）。这次，k代表所有可能的单词的数量，它是相当大的。我们基于 sVOLE 的 MatrixMul 不适合这里，因此我们采用基于 AHE 的 MatrixMul [25]。

TopK。为了保持多样性和准确性之间的平衡，从$y_0$中选择K个最大值：
$$
\langle \mathbf{y}_1\rangle\leftarrow\Pi_{TopK}(\langle\mathbf{y}_0\rangle),\mathbf{y}_1\in\mathbb{Z}_{2^l}^K
$$

这部分由第五章提出的协议来完成。

温度。温度T决定了GPT生成文本的创造性和多样性：较高的温度（例如，$T=1.5$）会产生更加多样化和创造性的文本，而较低的温度（例如，$T=0.5$）会产生更加集中和确定性的文本。它是由 S 持有的超参数，并与$y_1$中的每个值相乘。这可以通过 AHE 轻松实现：
1. C给S发送其AHE加密后的共享$E(\langle y_{1,1}\rangle_C),...,E(\langle y_{1,K}\rangle_C)$。实际上，我们通过将它们表示为RLWE密文的多项式系数来进行整体加密。
2. S将其份额添加到密文中：$E(\langle y_{1,1}\rangle_C+\langle y_{1,1}\rangle_S),...,E(\langle y_{1,K}\rangle_C+\langle y_{1,K}\rangle_S)$
3. S对所有密文乘T：$E(T\cdot y_{1,1}),...,E(T\cdot y_{1,K})$
4. S给所有密文加上随机数：$E(T\cdot y_{1,1}+r_1),...,E(T\cdot y_{1,K}+r_K)$
5. S将生成的密文返回C
6. S 解密密文，现在用 y2 表示的温度值是秘密共享的：
$$
\langle y_{2,i}\rangle_C:=T\cdot y_{1,i}+r_i,\langle y_{2,i}\rangle_S:-r_i,\forall i\in[K].
$$

随机采样。对$y_2$进行softmax运算，得到$y_3$表示的概率向量，然后根据该概率向量对响应词进行随机采样。这种随机采样确保生成的输出既多样化又与上下文相关。

我们采用第 6 节中描述的安全采样协议来获取索引：$\langle j \rangle\leftarrow\Pi_{Sample}(y_3)$

考虑到词向量是公开的，如果C学习了索引，它就可以从词向量中检索出最终的响应词。然而，天真地将 j 透露给 C 有两个问题：
- 在算法3（第1 行）中，值v 由C 采样。因此，如果 C 学习了 j，它可能会获得有关输入 x 的一些信息。 
- 索引j 不是正确的索引，因为概率向量已在算法2（第1 行）中被打乱。

回想一下，算法 2 中的洗牌过程大致如下：
1. C生成随机排列$\pi_C$；S和C共同将$\pi_C$应用于输入向量，获得相应的秘密份额。
2. S生成随机排列$pi_S$； S和C共同将$pi_S$应用于$pi_C$的输出，获得相应的秘密份额。

一个关键的观察结果是算法3第11行的`i`是公开的。为此，我们让S计算$i':=\pi_{S}^{-1}(i)$并秘密共享$i'$。然后我们将算法3的第11行替换为：
$$
\langle j\rangle:=\sum_{i=1}^K F_{MUX}(\langle i'\rangle,\langle b'_i\rangle^l)
$$
由此，向C透漏j不会透露有关输入的任何信息，因为C不知道$\pi_{S}^{-1}(i)$。获得j后，C计算$j':=\pi_C^{-1}(j)$，这是词向量中的正确索引。

## 实验评估