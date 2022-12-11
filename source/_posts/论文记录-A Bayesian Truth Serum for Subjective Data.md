---
title: 论文记录-A Bayesian Truth Serum for Subjective Data
date: 2022-10-24 16:33:40
tags:
    - game theory
    - trustworthiness
categories: 论文
description: 贝叶斯实话血清
---

# A Bayesian Truth Serum for Subjective Data

## Abstract
因为没有评估真实性的公共标准，因此主观判断作为科学和政策的重要信息来源是不可靠的。本文提出了一种在客观事实不可知的情况下获取真实主观数据的评分方法。这种方法不是传统的少数服从多数，而是选择那些比集体预测更常见的答案。这种对评分标准的简单调整，消除了所有有利于多数人的偏见，即使是那些认为自己的答案代表了少数人的观点的受访者，真实的答案也会使预期得分最大化。

## Intro
主观数据的重要性略

主观数据的价值受限于数据源的质量，但又没有一个标准来评价这个质量

本文提出了一种诱导主观信息的方法，是为客观事实本质上或实际上不可知的情况而设计的(6)。该方法包括一个信息评分系统，从理性（即贝叶斯）期望值最大化的受访者样本中诱导出真实的答案。与其他贝叶斯诱导机制不同（7-9），该方法并不假定研究者知道不同回答之间的概率关系。因此，它可以由一个对该领域完全不了解的研究者应用于以前没有问过的问题。与早期没有答案的理论测试方法(5)或德尔菲法(10)不同的是，它并不推崇共识答案。因此，受访者没有理由将他们的答案偏向于可能的群体平均值。即使有人确信自己的答案代表了少数人的观点，真实的回答仍然是正确的策略。

简单来说就是不再采用多数人共识作为答案，而是采用比集体预测更常见的作为答案。
> 个人觉得就是用集体预测构建了一个先验，然后选比先验更常见的答案

这项评分标准针对单一问题，例如：
1. 你估计人类能活过2100年的概率是多少(概率以百分比计量) ？
2. 你会在下一届总统选举中投票吗(肯定/可能/可能不会/肯定不会) ？
3. 在过去的一年里，你是否有超过20个性伴侣(是/否)
4. 毕加索是你最喜欢的20世纪画家吗(是/否) ？

每个受访者都会提供一个个人答案，并预测答案的经验分布(即，支持每个答案的人的比例)。本文对预测的准确性进行评分，也就是说，对它们与经验频率的匹配程度进行评分。作为主要关注对象的个人答案，其评分标准是令人惊讶的普遍性。在预测频率为5%的情况下，一个被10%的人认可的答案将是令人惊讶的普遍现象，并将获得较高的信息分；如果预测的平均频率为25%，这将是一个令人惊讶的不常见的答案，因此获得较低的分数。

该方法利用了关于人口频率的贝叶斯推理中一个被忽视的含义。在大多数情况下，人们应该预期其他人会低估自己的意见或个人特征的真实频率。这一含义是更常见的贝叶斯论证的必然结果，即对某一特定意见或特征在人群中的频率的最高预测应该来自持有该意见或特征的个人，因为持有该意见构成了一个关于其流行程度的有效和有利的信号。例如，将毕加索评为自己最喜欢画家的人应该——而且通常也是如此——对持有该观点的人群的百分比给出更高的估计，因为他们自己的感受是一个有信息量的样本。因此，毕加索爱好者有理由相信，与其他人的估计相比，他们对毕加索受欢迎程度的最佳估计会更高，他们会认为，毕加索的真实受欢迎程度被人们低估了。因此，一个人的真实意见也是最有可能出人意料的意见。（最后这句话和前文有什么关系我没理解）

这个结论的有效性并不取决于个人真实的答案是否被认为是罕见的或广泛的。例如，一个回答问题时有超过20个性伴侣的男性可能觉得很少有人属于这种滥交的类别。然而，根据贝叶斯推理，他应该期望他个人对这个百分比的估计（例如5%）会比从整个人口中收集的估计的平均值（例如2%）高一些。他有超过20个性伴侣的事实证明，包括伴侣较少的人在内的一般人群会低估这种情况的普遍性。

在说真话能最大化自己的期望信息分（也就是前面说的那个评分系统）时，说真话是个体理性和集体理性，是唯一的均衡。

均衡结果依赖于两个假设：
1. 受访者数量必须足够大，使得单个回答不会影响经验分布。这些结果对于大的有限种群来说确实成立，但对于可数的无限种群来说，说明起来更简单，就像这里所做的那样。受访者以$r\in \lbrace 1,2,... \rbrace$为索引，他们针对多选项问题的真实回答记为$t^r=(t_1^r,...,t_m^r)(t_k^r\in\lbrace 0,1\rbrace, \sum_k x_k^r=1)$。$t_k^r$表示第$k$个回答是否是第$r$个受访者的真实答案，如果是则值为1，否则为0。真实答案也被称为个人意见或特点。
2. 受访者将个人意见视为关于人口分布的非个人信息信号，这是一个未知参数，$\omega = (\omega_1,...,\omega_m)\in \Omega$。形式上，本文假设所有受访者的公共知识是：后验信念$p(\omega |t^s)$ 都服从于$\omega$的单一分布的贝叶斯更新，也称之为公共先验，记作$p(\omega)$，且，当且仅当$t^r=t^s$时$p(\omega|t^r)=p(\omega|t^s)$。因此，个人意见提供了关于$\omega$的证据，但推论是非个人的：受访者相信其他与他们意见相同的人也会得出关于人口频率的相同推论。因此，我们可以用$t_j$表示意见为$j$的受访者，并在联合概率和条件概率中取消受访者上标：$Prob\lbrace t_j^r=1 |t_j^s=1\rbrace$变为$p(t_j|t_i)$，其他的也类似改写。

## 关于先验和后验的一个例子
对于一个二值问题，一个人可以如下推断模型。每一个受访者私下进行一次有偏硬币抛掷，它出现头像那一面的概率是$\omega_H$。抛掷结果代表他的个人意见。以此为基准点，他构造了一个后验分布，$p(\omega_H|t^r)$，其期望是预测的出现头像的频率。例如，如果先验是均匀分布，则抛掷硬币后的后验分布是$[0,1]$的三角分布，倾向于正面还是反面取决于硬币结果，期望值是$\frac{1}{3}$或$\frac{2}{3}$。如果先验不是均匀分布，而是强烈地偏向于相反的结果(例如，反面) ，那么在抛出头像那面之后，头像的预期频率可能仍然相当低——这对应于一些一看就不常见的特征，例如一年有超过20个性伴侣。

> 在接下来的内容中，我们把硬币头像那一面称为正面(head)，另一边成为反面(tail)。

从先验计算后验的过程如下：
首先，贝叶斯公式为：
$$
P(H|D)=\frac{P(D|H)P(H)}{P(D)}
$$
其中，`H`是假设，`D`是数据，`P(H)`是先验概率（先验概率顾名思义是看到数据前的猜测），`P(H|D)`是后验概率（后验概率顾名思义是拿到数据之后的猜测），`P(D)`是数据发生的概率，`P(D|H)`是在这个假设下数据发生的概率，也叫似然函数 。 而`P(D)`和`P(D|H)`的关系是：
$$ 
P(D)=\sum_{all\ H}[P(D \mid H) \times P(H)] 
$$
最初硬币是公平和不公平的概率分别是0.5，前者抛出正面和反面的概率也分别是0.5，后者抛出正面和反面的概率分别是1和0。我们把几个事件用如下字母表示：
1. 硬币是公平的——A
2. 硬币是不公平的——B
3. 硬币扔出正面——H
4. 硬币扔出反面——T

则显然，$P(A)=P(B)=0.5$。

如果一个受访者扔出了正面，则贝叶斯公式中的`D`为`正面`，在`A`和`B`两个事件发生的情况下，扔出正面的概率为：
$P(D|A)=0.5, P(D|B)=1$.

我们的要求是已知`D=正面`的情况下，判断这个硬币是`A`和`B`的概率分别有多少，也就是说要计算`P(A|D)`和`P(B|D)`。

利用贝叶斯公式，我们还有一个变量`P(D)`不知道，但是可以如下计算：
$$
P(D)=P(D|A)P(A)+P(D|B)P(B)=0.5*0.5+1*0.5=0.75
$$
$$
P(A|D)=\frac{P(D|A)P(A)}{P(D)}=0.5*0.5/0.75=\frac{1}{3}
$$
$$
P(B|D)=\frac{P(D|B)P(B)}{P(D)}=1*0.5/0.75=\frac{2}{3}
$$
也就是说，在扔出正面之后，认为硬币是公平的概率会变成$\frac{1}{3}$，而认为硬币不公平的概率会变成$\frac{2}{3}$。

而如果扔出了反面，从直觉上来说就可以判断出硬币肯定是公平的，从数学计算上来说，当`D=反面`时，$P(D|A)=0.5, P(D|B)=0$.
$$
P(D)=P(D|A)P(A)+P(D|B)P(B)=0.5*0.5+0*0.5=0.25
$$
$$
P(A|D)=\frac{P(D|A)P(A)}{P(D)}=0.5*0.5/0.25=1
$$
$$
P(B|D)=\frac{P(D|B)P(B)}{P(D)}=0*0.5/0.75=0
$$
可以看出来扔出反面后认为硬币是公平的概率会变成1，而不公平的概率会变成0.

## 具体计算方法
该方法的一个重要简化是不提取先验/后验分布，只提取答案和预测频率，答案和预测分别记作$x^r=(x_1^r,...,x_m^r)(x_k^r\in\lbrace 0,1\rbrace,\sum_k x_k^r=1)$和$y^r=(y_1^r,...,y_m^r)(y_k^r\geq 0,\sum_ky_k^r=1)$。我们按照下列公式计算人口认可频率$\bar{x_k}$，和预测频率的（几何）平均$\bar{y_k}$
$$
\bar{x}_{k}=\lim _{n \rightarrow \infty} \frac{1}{n} \sum_{r=1}^{n} x_{k}^{r},
$$
$$
\log \bar{y}_{k}=\lim _{n \rightarrow \infty} \frac{1}{n} \sum_{r=1}^{n} \log y_{k}^{r}
$$
说明：这里的$\bar{x_k}$就是计算选择这个回答的人占总人数的比例，而$\bar{y_k}$则是所有人对这个回答的预测频率的几何平均值

我们不使用预设的答案键，而是根据答案的信息得分来评估答案，这个得分就是实际频率与预测频率的对数比。
$$ \log \frac{\bar{x}_{k}}{\bar{y}_{k}} $$
至少一个答案的信息分非负。预测中的偏差往往会降低所有$\bar{y_k}$值，从而提高信息分值。

受访者的总分将信息分数与预测准确性的单独分数结合起来：
受访者$r$的得分 = 信息分 + 预测分 = 
$$\sum_{k} x_{k}^{r} \log \frac{\bar{x}_{k}}{\bar{y}_{k}}+\alpha \sum_{k} \bar{x}_{k} \log \frac{y_{k}^{r}}{\bar{x}_{k}}, 0<\alpha $$
上述公式是博弈的完全收益方程，它是对称的，如果 a = 1则为零和。第一部分是信息分，除了被受访者$r$选择的那个答案以外，其他答案的$x_k^r$都是`0`。第二部分是与经验分布和受访者$r$对该分布的预测之间的相对熵成正比的惩罚。最优预测分为0，是指预测完全符合现实，即$y_k^r=\bar{x_k}$（也就是说，所有人对这个答案出现的频率预测都一致并且符合真实情况）。期望预测分可以通过报告期望频率$y_k^r=E\lbrace \bar{x_k}|t^r \rbrace$来最大化。系数$\alpha$表示预测误差的权重。

接下来用硬币的例子来说明该公式是如何发挥作用的。想象有下述两种等可能性的事件：
1. 硬币是公平的
2. 硬币不公平，它永远会扔出正面
在这个表述里，硬币是公平的先验概率为$\frac{1}{2}$

接下来找一群人，让他们私下抛硬币并报告自己是哪一面，则可以分析出以下两种情况：
1. 一个扔出了反面的观测者会意识到这个硬币属于第一种情况，并由此预测整个群体观察到的正面和反面频率是五五开的。
2. 一个扔出了正面的观测者会把“硬币是公平的”这件事的概率从先验的$\frac{1}{2}$降低到后验的$\frac{1}{3}$，进而使得他对扔出反面的预期概率降低到$\frac{1}{6}$，而扔出正面的预期概率就是$\frac{5}{6}$。

我们从扔出反面的观测者的角度思考问题，其他人对抛硬币出反面这一结果的预期频率应该是$\frac{1}{2}$和$\frac{1}{6}$的混合，也就是说比他自己预测的$\frac{1}{2}$要小，因此他会期待实际结果中的反面更常见，从而得到较高的信息分。而相反，他会希望正面不常见，因为$\frac{1}{2}$和$\frac{5}{6}$的混合比他自己预测的$\frac{1}{2}$要大，这会导致一个较低的信息分。

类似的，扔出正面的观测者就会希望实际结果中正面更常见。由此可以发现，大家都会希望自己扔出的那一面更常见，而为此也会如实上报自己的结果从而试图增加这部分信息分。

该示例说明了信息分的一般属性。也就是说，如果最佳答案是由预期的信息得分精确定义的且其他受访者如实回答并给出真实的预测频率，那么一个真实的答案构成了对最令人惊讶的常见答案的最佳猜测。此属性不依赖于可能的答案的数量或先验。它直接导致均衡结果。

假设：
1. 每一个意见为$t^r$的受访者$r$通过对共同的先验$p(\omega)$应用贝叶斯法则形成了对意见的群体分布的后验$p(\omega|t^r)$。
2. 当且仅当$t^r=t^s$时，$p(\omega|t^r)=p(\omega|t^s)$
3. 分数按照前面的那个信息分+预测分来计算

则：
1. (T1) 对于任意$\alpha>0$，说真话是纳什均衡：说真话可以最大化每一个相信其他人也会说真话的受访者的期望总分。
2. (T2) 期望均衡信息分是非负的，并且在所有受访者都说真话时取最大值。
3. (T3) 当$\alpha=1$时，博弈是零和的，总分在说真话均衡中为$log p(\omega|t^r)+K$，$K$由零和约束设置。

说真话是指回答和预测都是真实的，即：$x^r=t^r$且$y^r=E\lbrace \omega|t^r \rbrace$。

T2指出，尽管存在其他均衡，可以通过将多个真实意见映射到一个反应类别中或通过随机化来构建，但这些揭示性较低的均衡会导致所有受访者的信息得分较低。如果需要，我们可以通过在公式2中给予信息分相对更多的权重来增强讲真话的战略优势。在$\alpha$足够小的情况下，讲真话均衡中的预期总分将以帕累托方式支配任何其他均衡中的预期分值。

T3说明，通过设置$\alpha=1$，我们可以将该博弈呈现为一个纯粹的竞争性的零和竞赛。总分根据受访者对答案的真实分布的预期程度进行排名。需要注意的是：评分系统只要求提供真实答案的预期分布$E\lbrace \omega|t^r \rbrace$而不是后验分布$p(\omega|t^r)$，后者是一个`m`维的概率密度函数。我们可以通过一个不直接引出这些概率的方法来推断哪些受访者对$\alpha$的实际值赋予了更多的概率。

在以往关于激励机制的经济学研究中，标准的做法是假设评分者（或中心）知道先验和后验，并将这种知识纳入评分函数。原则上，先验的任何变化，无论是问题措辞的变化、样本构成的变化，还是新的公共信息的变化，都需要重新计算计分函数。相比之下，我的方法采用了一个通用的一刀切的打分方程，不提先验或后验概率。这对实际应用有三个好处。首先，问题不需要局限于一些预先测试过的、可获得经验估计的基本比率和条件概率的集合；相反，人们可以利用自然语言的全部资源，为每个应用定制一套新问题。第二，可以对不同的人群进行相同的调查，或者在动态环境下进行调查（这与政治民意调查有关）。第三，人们可以诚实地指示受访者在制定自己的答案时不要猜测他人的答案。真实的答案对任何先验来说都是最佳的，而且没有公开的概率供他们考虑，或许还可以拒绝。

当涉及到为复杂、独特的问题打分时，这些都是决定性的优势。特别是，人们可以应用这种方法来获得关于任何明确陈述的命题的真实价值的诚实的概率判断，即使实际的真理是遥不可及的，也没有先验。例如，最近由一位著名的英国天文学家撰写的《我们最后的世纪》一书中，人类在2100年后生存的机会不超过50：50。这是一个挑衅性的评估，它不会很快被检验。用现在的方法，人们可以提出这样的问题：这是我们最后的世纪吗？并将其提交给一个专家样本，他们将各自提供一个主观概率，并估计其他概率的概率分布。T1意味着对主观概率的诚实报告将使预期信息得分最大化。专家们将面临类似的讲真话的激励，就像他们对实际结果下注一样，例如，在期货市场上，结果可以及时确定，以便评分。

我们用离散计算来说明。假设概率以1%为精度被分为100份，由此这个问题就成了一个有100个选项的选择题（在实际生活中，我们往往会有更少的类别，和更平滑的经验频率）。人口向量$\omega=\lbrace \omega_{00},...,\omega_{99} \rbrace$表示专家们之间的关于概率的未知分布。给定任意先验，$p(\omega)$，计算作为真实个人概率和被大众所认可的概率的函数的预期信息分数是一项费力但简单的工作。计算结果如下图所示，其中`A(90)`和`B(90)`分别表示两种不同先验（分别记作$p_A(\omega)$和$p_B(\omega)$）下专家认为90%的概率地球会在2100年之前毁灭。因此，专家们有着相同的评估，但对于他们的评估与其他人的评估之间的关系却有着不同的理论。虽然A90和B90不同，但在这两种情况下，预期信息分都是由90%的真实认可来计算的。这证实了T1。在这两种情况下，每个专家都认为他的主观概率相对于人群是悲观的。以个人估计的90%为条件，对他人概率的期望值在$p_A(\omega)$的情况下只有65%，在$p_A(\omega)$的情况下只有54%。

如果主观概率变成`50%`，这两条线会移动到`A(50)`和`B(50)`，则此时二者的最优策略就也会变成`50%`。总的来说就是无论主观信念怎么变，说实话都是最好的选择。

A线和B线的不同之处在于，A线的先验概率被假定为50%，B线为20%。B线的期望分数较高，因为90%的估计值在这种情况下更令人惊讶。
![概率统计图](https://github.com/likun1208/image/blob/master/BayesianTruth.jpg?raw=true)

假设评分者能精确为估计值打分，则对比信息分与这个精确分是有意义的。激发关于可公开验证的事件的诚实概率的标准工具是对数适当得分规则。根据该规则，一个专家在`n`个互斥事件上宣布其概率分布为$z=(z_1,...,z_n)$，如果事件$i$发生了，则他得到的评分为$K+log z_i$。举个例子，一位专家对人类将在2100年之前灭亡的真实主观概率估计为90%，但他宣布的概率`z`可能不是90%，根据`z`可以计算出他的分数是$0.9log z+0.1log(1-z)$，再次强调，这里假设我们知道他真实的估计概率是90%。这个分数计算公式会在$z=0.9$时取最大值，就是图中PS90对应的那条线。可以看出来它和用信息分计算的A90、B90的趋势是基本一样的。

非个人信息性先验的假设可能在两方面会失败。首先，在存在关于人口频率的公共信息的情况下，一个真实的答案可能不会对这些频率提供信息。例如，一个人的性别对他们判断人口中的男女比例的影响很小。这意味着$t^r \neq t^s$但$p(\omega|t^r)=p(\omega|t^s)$，且诚实和欺骗性答案的预期信息分之间的差异几乎为零（尽管仍然是正数）。如下图所示，补救措施是将性别问题与一个与性别有关的意见问题结合起来。

第二，具有不同品味或特征的受访者可能出于不同的原因选择相同的答案，从而形成不同的后验。例如，具有非标准政治观点的人可能会把他或她对某位候选人的喜欢当作大多数人都会喜欢其他候选人的证据。这意味着尽管$t^r=t^s$，但是$p(\omega|t^r) \neq p(\omega|t^s)$。在这里，补救的办法也是扩大问卷，让当事人同时透露意见和特征。

最后一个例子，一个艺术评估，说明了这两种补救措施。这个例子假设存在专家和门外汉，以及一个二元性质的状态：一个特定的艺术家是否代表原创才能的问题。根据假设，艺术专家能很好地识别这种区别，但普通人的辨别能力很差，事实上，喜欢衍生艺术家的机会比喜欢原创艺术家的机会要大。专家的比例是众所周知的，其他的概率也是如此（表1）。

本例和其他例子中的信息分反映了与某一观点或特征相关的信息量。在表格中，专家有明显的优势，尽管他们在样本中占少数，因为他们的意见对人口频率的信息量更大。一般来说，意见`i`的期望信息分就等于分布$p(\omega|t_k,t_i)$和$p(\omega|t_k)$之间的期望相对熵，后者是所有$t_k$的平均值。换句话说，`i`的预期得分是信息论的衡量标准，即赞同`i`的意见会在多大程度上改变他人对人口分布的后验信念。专家的认可将导致信念的更大转变，因为它对驱动两个部分意见的基本变量的信息量更大。这种影响的衡量标准对专家群体的规模或专家与非专家意见之间的关联方向相当不敏感。

![表格](https://github.com/likun1208/image/blob/master/BayesianTruth-2.png?raw=true)

通过建立说实话的激励机制，我并不是说人们在没有明确的经济回报的情况下就会欺骗或者不愿意提供信息。相反，人们担心的是，缺乏外部标准可能会助长自我欺骗和虚假的信心，即使是在善意的人之间。一个未来学家，或者一个艺术评论家，可以轻松地花一生的时间做出判断，而不用面对医生、科学家或者商业投资者的现实检查。在缺乏现实检验的情况下，人们很容易给予主流共识以特殊地位。明确得分的好处恰恰是抵消了非正式的压力，要求同意(或者也许是要突出(和不同意)。事实上，仅仅存在一个真相诱导评分系统就为社会科学提供了方法论上的保证，表明如果需要的话，主观数据可以通过一个既不基于信仰(所有的答案都同样好)也不偏向于例外观点的过程来获得。

## 我的问题
1. 公式里的$\bar{y_k}$如果是0怎么办？
2. 信息分那里为什么要取对数？如果不取对数，效果是不是仍然一样？大小关系感觉没变。

## 补充材料
是这篇论文作者附的一系列证明。

有一个无限可数的受访者群体，记作$r,s,...\in I=\lbrace 1,2,...\rbrace$，它们都面对同一个有着$m$个候选答案的多选项问题。受访者$r$的私有“信号”通过$m$维单位向量$t^r\in E^m$提供，该向量中，坐标对应了受访者$r$所选答案的元素的值是1，其他元素值为0。持有意见$i$的受访者记作$t_i$。

说明：我们用上标来指示个体，下标指示个体的类别，即$t^r$表示某个具体的受访者$r\in I$的意见，而$t_i$表示持有意见$i$的受访者。

每个受访者支持一个回答，并预测其他受访者中支持其他可能回答的频率。回答用$n$维单位向量$x^r=(x_1^r,...,x_m^r)\in E^m,(x_k^r\in\lbrace 0,1 \rbrace,\sum_k x_k^r=1)$来表示，而预测用相对频率分布$y^r=(y_1^r,...,y_m^r)\in \Delta^m(y_l^r\geq 0,\sum_k y_k^r=1)$来表示。$x_k^r$根据个体$r$是否支持这个答案而取值1或者0；$y_k^r$是个体$r$对支持答案$k$的受访者的比例的估计。$(x,y)$是回答和预测组成的向量（可数无限）

任意受访者的得分取决于他的回答、预测和经验平均：
$$
\begin{array}{c}
\bar{x}_{k}=\lim _{n \rightarrow \infty} \frac{1}{n} \sum_{r=1}^{n} x_{k}^{r}, \\
\log \bar{y}_{k}=\lim _{n \rightarrow \infty} \frac{1}{n} \sum_{r=1}^{n} \log y_{k}^{r} .
\end{array}
$$
其中，$\bar{x_k}$是答案$k$的平均频率，$\bar{y_k}$是回应$k$的预测频率的几何平均。如果$y_k^r=0$，则设$\bar{y_k}=0$，且设$log(0/0)=0$，$0log(0)=0$。

受访者$r$的得分，是$(x,y)$的函数，为：
$$
u^{r}(x, y)=\sum_{k} x_{k}^{r} \log \frac{\bar{x}_{k}}{\bar{y}_{k}}+\alpha \sum_{k} \bar{x}_{k} \log \frac{y_{k}^{r}}{\bar{x}_{k}}
$$
第一项为信息分，是受访者$r$所支持的答案对应的$log(\frac{\bar{x_k}}{\bar{y_k}})$。第二项是预测分。

### 信息假设
1. A1：存在集合S的成员意见的公共先验$p(t^r,...,t^s)$。
2. A2：先验是可交换的：对于所有组合$\pi$，都有$p(t^r,...,t^s)=p(t^{\pi(r)},...,t^{\pi(s)})$。
3. A3：不同的意见代表不同的后验分布：$p(\cdot|t^r)=p(\cdot|t^s)$就意味着$t^r=t^s$。

A1是贝叶斯博弈理论的标准假设。A2是至关重要的。根据De Finetti定理，它意味着$\Omega=\Delta^m$上存在一个概率分布$p(\omega)$，它将共同先验表达为条件独立随机变量的联合分布：
$$
\begin{array}{l} 
p\left(t^{r}, ..., t^{S}\right)=\int_\Omega \prod_{q \in S} p\left(t^{q} \mid \omega\right) p(\omega) d \omega \\
p\left(t_{k}^{r} \mid \omega\right)=\omega_{k}=\lim _{n \rightarrow \infty} \frac{1}{n} \sum_{q=1}^{n} t_{k}^{q} .
\end{array}
$$
换句话说，受访者相信他们的意见是独立的，是以意见的人口频率$\omega$为条件的。条件独立性的假设可以直接引用，而不是从可交换性中推导出来。然而，交换性强调了关键的基本属性，即具有相同意见的受访者对人口中的意见分布具有相同的后验信念。最后一个假设（A3）是随机相关性的一个版本；它只影响到讲真话的纳什均衡是否严格。

### 贝叶斯纳什均衡策略
受访者$r$的回答策略是一个函数$x^r(t^r)=(x_1^r(t^r),...,x_m^r(t^r)):E^m \rightarrow \Delta^m$，表示如果受访者$r$的真实回答是$t^r$，则他有概率$x_k^r(t^r)$给出答案$x_k$。如果$x^r(t^r)=t^r$则该策略是真实的。

受访者$r$的预测策略同样是一个函数$y^{r}\left(t^{r}\right)=\left(y_{1}^{r}\left(t^{r}\right), \ldots, y_{m}^{r}\left(t^{r}\right)\right): E^{m} \rightarrow \Delta^{m}$，表示如果受访者$r$持有意见$t^r$，则他会预测人群中有占比$y_k^r$的人会回答$k$。我们不需要考虑预测随机性，因为收益函数是关于$y_k^r$的严格凸函数(convex)。

二元组$(x^r(t^r),y^r(t^r))$是受访者$r$的策略。$(x(t),y(t))$表示所有受访者的策略。$(x^{-r}(t^{-r}),y^{-r}(t^{-r}))$是除了受访者$r$以外其他所有受访者的策略。如果所有的答案都是真实的，并且预测符合贝叶斯法则，那么$(x(t),y(t))$就是集体真实的。

**定义**：如果对于所有受访者$r$、回答$x_k^r$和预测$y^r$，下列式子都成立，则$(x(t),y(t))$是贝叶斯纳什均衡(BNE)。
$$
E\left\{u^{r}(x(t), y(t)) \mid t^{r}\right\} \geq E\left\{u^{r}\left(x^{r}, y^{r}, x^{-r}\left(t^{r}\right), y^{-r}\left(t^{r}\right)\right) \mid t^{r}\right\}, \text { for all } x^{r} \in E^{m}, y^{r} \in \Delta^{m}
$$

如果不等式是严格的，则该BNE也是严格的

**定理1**：如果A1~A3成立，则集体说真话是严格贝叶斯纳什均衡。
证明：如果大家都说真话，则人口平均是：
$$
\begin{aligned}
\bar{x}_{k} & =\lim _{n \rightarrow \infty} \frac{1}{n} \sum_{s=1}^{n} x_{k}^{s} =\lim _{n \rightarrow \infty} \frac{1}{n} \sum_{s=1}^{n} t_{k}^{s} =\omega_{k},\\

\log \bar{y}_{k} & =\lim _{n \rightarrow \infty} \frac{1}{n} \sum_{s=1}^{n} \log y_{k}^{s} 
=\lim _{n \rightarrow \infty} \frac{1}{n} \sum_{s=1}^{n} \log p\left(t_{k} \mid t^{s}\right) 
=\sum_{j=1}^{n} \omega_{j} \log p\left(t_{k} \mid t_{j}\right)
\end{aligned}
$$

考虑持有意见$i$且相信其他人都说真话的个体，他应该支持哪个答案才能最大化自己的期望评分呢？因为期望预测错误并不取决于他自己的回答，我们可以先忽略评分公式的第二部分，只看第一部分信息分。如果他支持答案$j$，则他的期望信息分为：
$$
\begin{aligned}
E\left\{\log \frac{\bar{x}_{j}}{\bar{y}_{j}} \mid t_{i}\right\} & =\int_{\Omega} p\left(\omega \mid t_{i}\right) E\left\{\log \frac{\bar{x}_{j}}{\bar{y}_{j}} \mid \omega\right\} d \omega \\
& =\int_{\Omega} p\left(\omega \mid t_{i}\right) \sum_{k=1}^{m} \omega_{k} \log \frac{\omega_{j}}{p\left(t_{j} \mid t_{k}\right)} d \omega \\
& =\sum_{k=1}^{m} \int_{\Omega} p\left(\omega, t_{k} \mid t_{i}\right) \log \frac{\omega_{j}}{p\left(t_{j} \mid t_{k}\right)} d \omega \\
& =\sum_{k=1}^{m} p\left(t_{k} \mid t_{i}\right) \int_{\Omega} p\left(\omega \mid t_{k}, t_{i}\right) \log \frac{\omega_{j}}{p\left(t_{j} \mid t_{k}\right)} d \omega \\
& =\sum_{k=1}^{m} p\left(t_{k} \mid t_{i}\right) \int_{\Omega} p\left(\omega \mid t_{k}, t_{i}\right) \log \frac{p\left(t_{j} \mid \omega\right) p\left(t_{k} \mid t_{j}, \omega\right)}{p\left(t_{j} \mid t_{k}\right) p\left(t_{k} \mid \omega\right)} d \omega \\
& =\sum_{k=1}^{m} p\left(t_{k} \mid t_{i}\right) \int_{\Omega} p\left(\omega \mid t_{k}, t_{i}\right) \log \frac{p\left(\omega \mid t_{k}, t_{j}\right)}{p\left(\omega \mid t_{k}\right)} d \omega
\end{aligned}
$$

这个公式第一行计算了先验分布$p(\omega|t_i)$下的$log(\frac{\bar{x_j}}{\bar{y_j}})$的期望，第二行是从集体如实上报推导而来，第三行使用条件独立将$\omega_k p(\omega|t_i)$改写为$p(\omega,t_k|t_i)$。第四行是使用贝叶斯规则从第三行推出的。第五行我们再次使用条件独立将$\omega_j$改写为$p(t_j|\omega)$，然后插入$\frac{p(t_k|t_j,\omega)}{p(t_k|\omega)}=1$。第六行也是随之根据贝叶斯规则推导而来。

由此，我们可以对比如实回答答案$i$和谎报答案$j$的信息分：
$$
\begin{array}{l} 
E\left\{\ln \frac{\bar{x}_{i}}{\bar{y}_{i}} \mid t_{i}\right\}-E\left\{\ln \frac{\bar{x}_{j}}{\bar{y}_{j}} \mid t_{i}\right\}=\sum_{k=1}^{m} p\left(t_{k} \mid t_{i}\right) \int_{\Omega} p\left(\omega \mid t_{k}, t_{i}\right) \log \frac{p\left(\omega \mid t_{k}, t_{i}\right)}{p\left(\omega \mid t_{k}\right)} d \omega \\
-\sum_{k=1}^{m} p\left(t_{k} \mid t_{i}\right) \int_{\Omega} p\left(\omega \mid t_{k}, t_{i}\right) \log \frac{p\left(\omega \mid t_{k}, t_{j}\right)}{p\left(\omega \mid t_{k}\right)} d \omega \\
=-\sum_{k=1}^{m} p\left(t_{k} \mid t_{i}\right) \int_{\Omega} p\left(\omega \mid t_{k}, t_{i}\right) \log \frac{p\left(\omega \mid t_{k}, t_{j}\right)}{p\left(\omega \mid t_{k}, t_{i}\right)} d \omega \\
>-\sum_{k=1}^{m} p\left(t_{k} \mid t_{i}\right) \log \left(\int_{\Omega} p\left(\omega \mid t_{k}, t_{i}\right) \frac{p\left(\omega \mid t_{k}, t_{j}\right)}{p\left(\omega \mid t_{k}, t_{i}\right)} d \omega\right) \\
=\sum_{k=1}^{m} p\left(t_{k} \mid t_{i}\right) \log \left(\int_{\Omega} p\left(\omega \mid t_{k}, t_{j}\right) d \omega\right)=\sum_{k=1}^{m} p\left(t_{k} \mid t_{i}\right) \ln (1)=0
\end{array}
$$


该不等式来源于Jensen不等式，且当A3成立时是严格的。它证明了当其他人都如实回答和预测时，说真话能最大化期望信息分。

接下来该证明同样应该如实预测。这次我们忽略信息分，来计算个体意见是$i$的条件下的期望预测分：
$$
\begin{aligned}
E\left\{\sum_{k=1}^{m} \bar{x}_{k} \log \frac{y_{k}}{\bar{x}_{k}} \mid t_{i}\right\} & =\sum_{k=1}^{m} E\left\{\bar{x}_{k} \mid t_{i}\right\} \log y_{k}-E\left\{\sum_{k=1}^{m} \bar{x}_{k} \log \bar{x}_{k} \mid t_{i}\right\} \\
& =\sum_{k=1}^{m} E\left\{\omega_{k} \mid t_{i}\right\} \log y_{k}-E\left\{\sum_{k=1}^{m} \omega_{k} \log \omega_{k} \mid t_{i}\right\} .
\end{aligned}
$$

这里假定会如实回答，因此$\bar{x_k}=\omega_k$。第二个期望不包括$y_k$，因此，能最大化评分的针对$k$个答案占比的预测，就是回答$k$的期望频率，或者说等价于随机选一个人会持有意见$k$的概率：$y_k=E\lbrace \omega_k|t_i\rbrace=p(t_k|t_i)$。

由此完成了集体说真话是BNE的证明。

**定理2**：如果A1~A3成立，则对于所有受访者而言下述内容都成立：
1. 任意BNE中的期望信息分是非负的
2. 说真话对应的均衡解下的期望信息分会微弱地高于其他任意BNE对应的期望信息分

证明(a)：期望信息分可以通过在定理1的公式那里设置$j=i$来计算：
$$
E\left\{\ln \frac{\bar{x}_{i}}{\bar{y}_{i}} \mid t_{i}\right\}=\sum_{k=1}^{m} p\left(t_{k} \mid t_{i}\right) \int_{\Omega} p\left(\omega \mid t_{k}, t_{i}\right) \ln \frac{p\left(\omega \mid t_{k}, t_{i}\right)}{p\left(\omega \mid t_{k}\right)} d \omega \geq 0
$$
这其实是分布$p(\omega|t_k,t_i)$和$p(\omega|t_k)$之间的相对熵，对所有$t_k$取平均。它衡量了“知道$t_i$”这件事能在多大程度上提升其他人对$\omega$的预测，也称为“数据期望效用”——将$t_i$看作数据（参考文献25的定理2.31）。当$i$的回答不会改变其他人对$\omega$的预测时，这个表达式最小为0。这证明了定理2的第一条。

证明(b)：我们考虑$q(\omega,\bar{x})$的联合分布，它是由一个特定的BNE推导而来的，该BNE记作$(x(t),y(t))$，$q$的边际与$p$重合，$q(\omega)=p(\omega)$。当真实意见是$i$而支持的答案是$j$时，期望信息分是：
$$
\begin{aligned}
E\left\{\log \frac{\bar{x}_{j}}{\bar{y}_{j}} \mid t_{i}\right\} & =\int_{\Omega} p\left(\omega \mid t_{i}\right) E\left\{\log \frac{\bar{x}_{j}}{\bar{y}_{j}} \mid \omega\right\} d \omega \\
& =\int_{\Omega} p\left(\omega \mid t_{i}\right) \sum_{k=1}^{m} \omega_{k} \log \frac{\bar{x}_{j}}{q\left(x_{j} \mid t_{k}\right)} d \omega \\
& =\sum_{k=1}^{m} p\left(t_{k} \mid t_{i}\right) \int_{\Omega} p\left(\omega \mid t_{k}, t_{i}\right) \log \frac{\bar{x}_{j}}{q\left(x_{j} \mid t_{k}\right)} d \omega \\
& =\sum_{k=1}^{m} p\left(t_{k} \mid t_{i}\right) \int_{\bar{X}} q\left(\bar{x} \mid t_{k}, t_{i}\right) \log \frac{\bar{x}_{j}}{q\left(x_{j} t_{k}\right)} d \bar{x} \\
& =\sum_{k=1}^{m} p\left(t_{k} \mid t_{i}\right) \int_{\bar{X}} q\left(\bar{x} \mid t_{k}, t_{i}\right) \log \frac{q\left(x_{j} \mid \bar{x}\right) q\left(t_{k} \mid x_{j} \bar{x}\right)}{q\left(x_{j} t_{k}\right) q\left(t_{k} \mid \bar{x}\right)} d \bar{x} \\
& =\sum_{k=1}^{m} p\left(t_{k} \mid t_{i}\right) \int_{\bar{X}} q\left(\bar{x} \mid t_{k}, t_{i}\right) \log \frac{q\left(\bar{x} \mid t_{k}, x_{j}\right)}{q\left(\bar{x} \mid t_{k}\right)} d \bar{x}
\end{aligned}
$$
这和定理1中的证明很像，除了第二行不再假设一个说真话的均衡。

现在我们将$q\left(\omega, \bar{x} \mid t_{i},t_{k}\right)$和$q\left(\omega, \bar{x} \mid t_{k}\right)$之间的相对熵扩展到两个不同的方式，先把$\omega$作为条件，再把$\bar{x}$作为条件：
$$
\begin{array}{l} 
\int_{\Omega, \bar{X}} q\left(\omega, \bar{x} \mid t_{k}, t_{i}\right) \log \frac{q\left(\omega, \bar{x} \mid t_{k}, t_{i}\right)}{q\left(\omega, \bar{x} \mid t_{k}\right)} d \omega d \bar{x}= \\
= \int_{\Omega} q\left(\omega \mid t_{k}, t_{i}\right) \log \frac{q\left(\omega \mid t_{k}, t_{i}\right)}{q\left(\omega \mid t_{k}\right)} d \omega \\
+\int_{\bar{X}, \Omega} q\left(\bar{x} \mid \omega, t_{k}, t_{i}\right) \log \frac{q\left(\bar{x} \mid \omega, t_{k}, t_{i}\right)}{q\left(\bar{x} \mid \omega, t_{k}\right)} d \bar{x} d \omega \\
= \int_{\bar{X}} q\left(\bar{x} \mid t_{k}, t_{i}\right) \log \frac{q\left(\bar{x} \mid t_{k}, t_{i}\right)}{q\left(\bar{x} \mid t_{k}\right)} d \bar{x} \\
\quad+\int_{\Omega, \bar{X}} q\left(\omega \mid \bar{x}, t_{k}, t_{i}\right) \log \frac{q\left(\omega \mid \bar{x}, t_{k}, t_{i}\right)}{q\left(\omega \mid \bar{x}, t_{k}\right)} d \omega d \bar{x}
\end{array}
$$
第二行仅考虑了$q(\omega,\bar{x})$对于$\omega$的边界，因此我们可以用$p$代替$q$；因为$\bar{x}$完全取决于$\omega$，所以第三行是0。现在我们可以将第二行如下改写，并将第四行如下扩展：
$$
\begin{array}{l} 
\int_{\Omega} p\left(\omega \mid t_{k}, t_{i}\right) \log \frac{p\left(\omega \mid t_{k}, t_{i}\right)}{p\left(\omega \mid t_{k}\right)} d \omega \\
= \int_{\bar{X}} q\left(\bar{x} \mid t_{k}, t_{i}\right) \log \frac{q\left(\bar{x} \mid t_{k}, x_{j}\right)}{q\left(\bar{x} \mid t_{k}\right)} d \bar{x} \\
\quad+\int_{\bar{X}} q\left(\bar{x} \mid t_{k}, t_{i}\right) \log \frac{q\left(\bar{x} \mid t_{k}, t_{i}\right)}{q\left(\bar{x} \mid t_{k}, x_{j}\right)} d \bar{x} \\
\quad+\int_{\Omega, \bar{X}} q\left(\omega \mid \bar{x}, t_{k}, t_{i}\right) \log \frac{q\left(\omega \mid \bar{x}, t_{k}, t_{i}\right)}{q\left(\omega \mid \bar{x}, t_{k}\right)} d \omega d \bar{x}
\end{array}
$$
对所有$t_k$求和可以得到一个表达式，该表达式关系到讲真话均衡中的预期信息分数和替代均衡中的预期信息分数。
$$
\begin{array}{l}
\sum_{k=1}^{m} p\left(t_{k} \mid t_{i}\right) \int_{\Omega} p\left(\omega \mid t_{k}, t_{i}\right) \log \frac{p\left(\omega \mid t_{k}, t_{i}\right)}{p\left(\omega \mid t_{k}\right)} d \omega \\
=\sum_{k=1}^{m} p\left(t_{k} \mid t_{i}\right) \int_{\bar{X}} q\left(\bar{x} \mid t_{k}, t_{i}\right) \log \frac{q\left(\bar{x} \mid t_{k}, x_{j}\right)}{q\left(\bar{x} \mid t_{k}\right)} d \bar{x} \\
+\sum_{k=1}^{m} p\left(t_{k} \mid t_{i}\right) \int_{\bar{X}} q\left(\bar{x} \mid t_{k}, t_{i}\right) \log \frac{q\left(\bar{x} \mid t_{k}, t_{i}\right)}{q\left(\bar{x} \mid t_{k}, x_{j}\right)} d \bar{x} \\
+\sum_{k=1}^{m} p\left(t_{k} \mid t_{i}\right) \int_{\Omega, \bar{X}} q\left(\omega \mid \bar{x}, t_{k}, t_{i}\right) \log \frac{q\left(\omega \mid \bar{x}, t_{k}, t_{i}\right)}{q\left(\omega \mid x, t_{k}\right)} d \omega d \bar{x}
\end{array}
$$
第一行是说真话均衡的期望信息分，第二行是在其他均衡中支持答案$j$的信息分，第三行和第四行是相对熵。这证明了期望信息分在不说真话的均衡里只会更低。

**定理3**：如果A1~A3成立，且$\alpha=1$，则该博弈是零和博弈，且在说真话的均衡中，持有意见$i$的受访者的总分为$log p(\omega|t_i)+K$。

证明：对于$\alpha=1$，评分公式为：
$$
u^{r}(x, y)=\sum_{k} x_{k}^{r} \log \frac{\bar{x}_{k}}{\bar{y}_{k}}+\sum_{k} \bar{x}_{k} \log \frac{y_{k}^{r}}{\bar{x}_{k}}
$$
所有受访者的平均总分比等于0：
$$
\begin{aligned}
\lim _{n \rightarrow \infty} \frac{1}{n} \sum_{r=1}^{n} u^{r}(x, y) & =\lim _{n \rightarrow \infty} \frac{1}{n}\left(\sum_{r=1}^{n} \sum_{k} x_{k}^{r} \log \frac{\bar{x}_{k}}{\bar{y}_{k}}\right)+\lim _{n \rightarrow \infty} \frac{1}{n}\left(\sum_{r=1}^{n} \sum_{k} \bar{x}_{k} \log \frac{y_{k}^{r}}{\bar{x}_{k}}\right) \\
& =\sum_{k}\left(\lim _{n \rightarrow \infty} \frac{1}{n} \sum_{r=1}^{n} x_{k}^{r}\right) \log \frac{\bar{x}_{k}}{\bar{y}_{k}}+\sum_{k} \bar{x}_{k}\left(\lim _{n \rightarrow \infty} \frac{1}{n} \sum_{r=1}^{n} \log \frac{y_{k}^{\prime}}{\bar{x}_{k}}\right) \\
& =\sum_{k} \bar{x}_{k} \log \frac{\bar{x}_{k}}{\bar{y}_{k}}+\sum_{k} \bar{x}_{k} \log \frac{\bar{y}_{k}}{\bar{x}_{k}}=0 .
\end{aligned}
$$
为了计算说真话均衡中的总分，我们需要用期望信息分的类似表达莱补充期望预测分，说真话均衡意味着最前面计算期望预测分的公式中的$y_k=p(t_k|t_i)$：
$$
\begin{aligned}
E\left\{\sum_{k=1}^{m} \bar{x}_{k} \log \frac{y_{k}}{\bar{x}_{k}} \mid t_{i}\right\} & =E\left\{\sum_{k=1}^{m} \omega_{k} \log \frac{p\left(t_{k} \mid t_{i}\right)}{\omega_{k}} \mid t_{i}\right\} \\
& =\int_{\Omega} p\left(\omega \mid t_{i}\right) \sum_{k=1}^{m} \omega_{k} \log \frac{p\left(t_{k} \mid t_{i}\right)}{\omega_{k}} d \omega \\
& =\sum_{k=1}^{m} \int_{\Omega} p\left(\omega, t_{k} \mid t_{i}\right) \log \frac{p\left(t_{k} \mid t_{i}\right)}{\omega_{k}} d \omega \\
& =\sum_{k=1}^{m} p\left(t_{k} \mid t_{i}\right) \int_{\Omega} p\left(\omega \mid t_{k}, t_{i}\right) \log \frac{p\left(t_{k} \mid t_{i}\right)}{\omega_{k}} d \omega \\
& =\sum_{k=1}^{m} p\left(t_{k} \mid t_{i}\right) \int_{\Omega} p\left(\omega \mid t_{k}, t_{i}\right) \log \frac{p\left(t_{k} \mid t_{i}\right) p\left(t_{k} \mid \omega\right)}{p\left(t_{k} \mid \omega\right) p\left(t_{k} \mid t_{i}, \omega\right)} d \omega \\
& =\sum_{k=1}^{m} p\left(t_{k} \mid t_{i}\right) \int_{\Omega} p\left(\omega \mid t_{k}, t_{i}\right) \log \frac{p\left(\omega \mid t_{i}\right)}{p\left(\omega \mid t_{k}, t_{i}\right)} d \omega \leq 0
\end{aligned}
$$
这也是对所有$t_k$取平均后，$p(\omega|t_k)$和$p(\omega|t_k,t_i)$的分布之间的相对熵。它测量了通过了解另一个人的意见$k$而产生的$i$对$\omega$的信念的（负数的）熵减。如果了解其他人的回答并不会改变$i$对$\omega$的信念，则期望预测分为0。

在说真话均衡解中，期望总分就是结合了前面的两组公式：
$$
\begin{aligned}
E\left\{\frac{\bar{x}_{i}}{\bar{y}_{i}}+\sum_{k=1}^{m} \bar{x}_{k} \log \frac{p\left(t_{k} \mid t_{i}\right)}{\bar{x}_{k}} \mid t_{i}\right\} & =\sum_{k=1}^{m} p\left(t_{k} \mid t_{i}\right) \int_{\Omega} p\left(\omega \mid t_{k}, t_{i}\right) \log \frac{p\left(\omega \mid t_{i}\right)}{p\left(\omega \mid t_{k}\right)} d \omega \\
& =\int_{\Omega} p\left(\omega \mid t_{i}\right) \sum_{k=1}^{m} \omega_{k} \log \frac{p\left(\omega \mid t_{i}\right)}{p\left(\omega \mid t_{k}\right)} d \omega
\end{aligned}
$$
那么，当人口平均是$\omega$时，支持$i$的人的后验评分为：
$$
\sum_{k=1}^{m} \omega_{k} \ln \frac{p\left(\omega \mid t_{i}\right)}{p\left(\omega \mid t_{k}\right)}=\ln p\left(\omega \mid t_{i}\right)-\sum_{k=1}^{m} \omega_{k} \ln p\left(\omega \mid t_{k}\right)
$$
第二项是由零和约束决定的常数。由此证明了定理3.

## 其他资料
[资料来源](https://wesselb.github.io/assets/write-ups/Bruinsma,%20A%20Bayesian%20Truth%20Serum.pdf)
假设一些人参加了一个带有主观问题的调查。你被要求对这个问题的答案分布做出最佳猜测。作为一个人，你的猜测并非完全不知情：你知道自己对这个调查问题的看法。尽管这个样本可能不能说明人口的分布情况，但它确实构成了一个有效的样本，因此应该说明一些问题；也就是说，在某种程度上，一个人的意见是一个信息丰富的 "一个样本"。

你的最优猜测记作$f$，它由每一个答案的频率组成。与人口群体的平均最优猜测即公共预测$\langle f\rangle$相比，可以预计$f-\langle f\rangle$会在你的意见取最高值，因为与常见的预测不同，你的最佳猜测是由你的意见提供的，尽管是非常轻微的；这种现象在实践中确实被观察到。因此，如果你是一个元理性的贝叶斯代理人，并意识到这种情况，你应该相信你的意见具有最高的概率，比通常预测的更常见。

贝叶斯吐真剂是基于上述现象的。在BTS中，答案的评分标准是衡量一个答案与常见预测值相比有多普遍。事实上，如上所述，从受访者的角度来看，诚实回答就能获得最高分的概率最大。 

### 模型
除了给出每个问题的回答$x^n$，BTS还要求受访者$n$给出他对该问题答案分布的最优猜测$f^n$，这些随机变量的模型如下图所示，

![BTS模型图](https://github.com/likun1208/image/blob/master/BTS-1.png?raw=true)

每一个个体$n$都持有意见$t^n$，我们将$t^n=i$简化表达为$t^n_i$。所有个体的意见都有条件独立，条件是给定潜在变量$\omega$，对于每个个体而言分布都是$p(t_i^n|\omega)=w_i$。此外，给定个体$n$的意见$t^n$，他们会根据回答策略$p(x^n|t^n)$来给出答案$x^n$，我们将$x^n=i$简化表达为$x^n_i$。个体$n$对其他回答$i$的频率预测记作$f^n_i$。需要注意的是，假设存在潜在变量$\omega$使得$p(t^n_i|\omega)=\omega_i$就意味着假设个体意见$t^n$服从一个可交换序列。

一般来说我们讨论哪一个个体都是无所谓的。为了简化，我们省略个体索引，例如$t_i$表示意见为$i$的个体，$x_j$表示回答为$j$的个体，$f_k$表示个体对回答$k$的频率预测为$f_k$。

【记录一下个人理解】BTS的模型应该是指，首先人群中持有的各个意见服从某个潜在分布$\omega$，在此基础上，随便一个受访者的个人意见是$i$的概率就记作$\omega_i$，而根据前文可知，个人意见会对回答和预测产生影响，从而使得受访者得出回答$x^n$和$f^n$，这俩应该都是向量，其中前者是受访者的答案对应的那个向量元素为1，其他都是0，表示他就选了这个答案；后者则是所有元素都小于1并且求和为1，表示后者对每个答案的频率预测，并且这些预测总和应该是1。

### 评分
给定回答$x^n$和预测频率$f^n$，BTS的评分为：
$$
\begin{aligned}
S\left(x_{i}, f\right) &=\overbrace{\log \frac{\langle x\rangle_{i}}{\langle f\rangle_{i}}}^{\text {information score }}-\overbrace{\sum_{j}\langle x\rangle_{j} \log \frac{f_{j}}{\langle x\rangle_{j}}}^{\text {prediction penalty }}, \\
\langle x\rangle_{i} &=\text { average of }\left(\mathbb{1}\left(x^{n}=i\right)\right), \\
\langle f\rangle &=\text { geometric average of }\left(f^{n}\right) .
\end{aligned}
$$
评分函数由两部分组成：
1. 信息分衡量的是答案$i$与公共预测相比的常见程度。
2. 预测惩罚形成了真实和预测的答案频率之间的Kullback-Leibler（KL）散度。

KL散度描述了我们用分布$Q$来估计数据的真实分布$P$的编码损失。

【记录一下个人理解】对比一下前面的公式，这里信息分那里不太一样，不过仔细分析一下会发现其实是一个意思，就是指【该答案的实际频率（anwser's actual frequency）】/【所有人对这个答案的预测频率的几何平均(mean of answer's predicted frequency)】。举个例子，有10个人，其中2个人选了这个答案，大家对这个答案的预测频率都是0.1（姑且这么假设），那信息分就是$log\frac{0.2}{0.1}$。信息分如果大于0（就是说里面的分式大于1），就意味着这个答案是出乎意料受欢迎的（surprissingly common），实际选它的人比大家对它的预测频率要高。

通常来说，如果一个人持有某种意见，那么他会产生偏见，从主观上认为持有该意见的人相对多，也就是说他自己的预测频率会比真实的频率高一些，有点类似信息茧房。从数学上来说，受访者提出的预测实际上是一个条件概率，条件就是他自己的意见，公式表达一下就是$P(我觉得大家有x的概率选A，y的概率选B，z的概率选C，等等|我的意见是B)$。举个例子，关于是否作弊这个问题，$P(你觉得很多人都作弊|你作弊了)>P（你觉得很多人都作弊|你没作弊）$。在这样的情况下，在预测总频率的时候，我们就会想：会有人不选这个答案，他们对这个答案的预测就会低一些。假如一个人本来觉得有20%的人会选这个答案，这样考虑一番之后他就会把自己预测的频率降低一些，比如降到15%。由此，信息分的分母就会变小，而此时这个人的真实意见就是那个出乎意料受欢迎的意见。

如何避免他人发言对偏见的影响

这里参考了[一个视频](https://www.youtube.com/watch?v=uOIqIeDFpS4)，不过感觉这个视频没太讲清楚，没说明白为什么说真话最好。

第二部分预测惩罚那里，是根据个人对所有答案频率的预测与所有答案真实频率的差距来计算的预测分，这个分越大，说明预测偏得越远。

### 属性
**假设3.1**：可交换性。个体意见$t^n$组成了一个可交换序列。该假设证明了BTS所假设的图形模型的合理性。

**假设3.2**：随机相关性。不同意见表示$\omega$的不同先验分布：如果$i\neq j$，则$p(\omega|t_i)\neq p(\omega|t_j)$。该假设是一个技术上的便利，将被用来得出最大化者的唯一性的结论。

**假设3.3**：样本量足够大。$\langle x\rangle$和$\langle f\rangle$的方差足够小从而可以用极限来求近似。

**定理3.1**：说真话是均衡解。假设一个受访者持有意见$k$、回答$i$和预测$f$，且其他人都如实回答和预测。则该受访者也最好如实回答和预测：
$$
\max _{(i, f)} \mathbb{E}\left(S\left(x_{i}, f\right) \mid t_{k}\right)=\left(k, p\left(t \mid t_{k}\right)\right)
$$
此外，
$$
\mathbb{E}\left(\text { information score }\left(x_{i}\right) \mid t_{i}\right)=\sum_{j} p\left(t_{j} \mid t_{i}\right) \mathrm{D}_{\mathrm{KL}}\left(p\left(\omega \mid t_{i}, t_{j}\right) \| p\left(\omega \mid t_{j}\right)\right) \text {. }
$$
证明：如果其他人如实回答和预测，则
$$
\langle x\rangle_{i} \approx p\left(t_{i} \mid \omega\right)=\omega_{i}, \quad \log \langle f\rangle_{i} \approx \sum_{j} p\left(t_{j} \mid \omega\right) \log p\left(t_{i} \mid t_{j}\right)=\sum_{j} \omega_{j} \log p\left(t_{i} \mid t_{j}\right)
$$
这里$\langle x\rangle_i$是人群中答案$i$的真实频率，如果大家都说真话，那么它应该就约等于那个潜在概率$w_i$。

而$\langle f\rangle_i$是人群对答案$i$的几何平均预测频率，根据几何平均数的公式，取对数后的计算方式就是每个元素取对数然后求和再平均，$p(t_i|t_j)$的含义看起来是说个人意见选$j$的个体对别人选$i$的频率预测，【每一种个人意见会出现的频率】乘log【该意见下的人对答案$i$的频率预测】，符合几何平均预测频率的定义。

信息分仅取决于回答$i$，而预测惩罚仅取决于预测$f$，因此我们可以分开考虑它们。

首先，
$$
\begin{aligned}
\mathbb{E}\left(\text { information score }\left(x_{i}\right) \mid t_{k}\right) & =\int p\left(\omega \mid t_{k}\right) \sum_{j} p\left(t_{j} \mid \omega\right) \log \frac{p\left(t_{i} \mid \omega\right)}{p\left(t_{i} \mid t_{j}\right)} \\
& =\sum_{j} p\left(t_{j} \mid t_{k}\right) \int p\left(\omega \mid t_{k}, t_{j}\right) \log \frac{p\left(\omega \mid t_{i}, t_{j}\right)}{p\left(\omega \mid t_{j}\right)} \mathrm{d} \omega \\
& \leq \sum_{j} p\left(t_{j} \mid t_{i}\right) \operatorname{D}_{\mathrm{KL}}\left(p\left(\omega \mid t_{i}, t_{j}\right) \| p\left(\omega \mid t_{j}\right)\right)
\end{aligned}
$$
当且仅当$k=i$时取等号。

第二，
$$
\begin{aligned}
\mathbb{E}\left(\text { prediction penalty }(f) \mid t_{k}\right) & =\int p\left(\omega \mid t_{k}\right) \sum_{j} p\left(t_{j} \mid \omega\right) \log \frac{f_{j}}{p\left(t_{j} \mid \omega\right)} \frac{p\left(t_{j} \mid t_{k}\right)}{p\left(t_{j} \mid t_{k}\right)} \mathrm{d} \omega \\
& =\int p\left(\omega \mid t_{k}, t_{j}\right) \sum_{j} p\left(t_{j} \mid t_{k}\right) \log \frac{f_{j}}{p\left(t_{j} \mid t_{k}\right)} \frac{p\left(\omega \mid t_{k}\right)}{p\left(\omega \mid t_{k}, t_{j}\right)} \mathrm{d} \omega \\
& \leq-\mathrm{D}_{\mathrm{KL}}\left(f_{j} \| p\left(t_{j} \mid t_{k}\right)\right)-\sum_{j} p\left(t_{j} \mid t_{k}\right) \mathrm{D}_{\mathrm{KL}}\left(p\left(\omega \mid t_{k}, t_{j}\right) \| p\left(\omega \mid t_{k}\right)\right)
\end{aligned}
$$
当且仅当$f_j=p(t_j|t_k)$时取等号。

定理3.1说明了说真话是贝叶斯纳什均衡。它还表明，讲真话的信息分数，也是最佳的信息分数，衡量的是另一个人的后验分布在得知你的意见后平均变化的程度；这表明专家可能享有更高的预期信息分数。下面的命题表明，讲真话也是最优贝叶斯纳什均衡。

**定理3.2**：说真话是最优均衡。说真话的均衡解是最大化期望信息分的均衡。

证明：在任意一个均衡中
$$
\langle x\rangle_{i} \approx p\left(x_{i} \mid \omega\right), \quad \log \langle f\rangle_{i} \approx \sum_{j} p\left(t_{j} \mid \omega\right) \log p\left(x_{i} \mid t_{j}\right)
$$
则：
$$
\begin{array}{l}
\left.\mathbb{E} \text { (information score }\left(x_{i}\right) \mid t_{k}\right) \\
\quad=\sum_{j} p\left(t_{j} \mid t_{k}\right) \int p\left(\omega \mid t_{k}, t_{j}\right) \log \frac{p\left(\omega \mid x_{i}, t_{j}\right)}{p\left(\omega \mid t_{j}\right)} \mathrm{d} \omega \\
\quad=\underbrace{\sum_{j} p\left(t_{j} \mid t_{k}\right) \int p\left(\omega \mid t_{k}, t_{j}\right) \log \frac{p\left(\omega \mid t_{k}, t_{j}\right)}{p\left(\omega \mid t_{j}\right)} \mathrm{d} \omega}_{\text {truth-telling equilibrium }}+\sum_{j} p\left(t_{j} \mid t_{k}\right) \underbrace{\int p\left(\omega \mid t_{k}, t_{j}\right) \log \frac{p\left(\omega \mid x_{i}, t_{j}\right)}{p\left(\omega \mid t_{k}, t_{j}\right)} \mathrm{d} \omega .}_{-\mathrm{D}_{\mathrm{KL}}\left(p\left(\omega \mid t_{k}, t_{j}\right) \| p\left(\omega \mid x_{i}, t_{j}\right)\right) \leq 0}
\end{array}
$$
最后，在说真话的均衡中，BTS是零和博弈，分数表征了与先验期望频率相比一个答案有多普遍。

**定理3.3**：零和博弈。在说真话博弈中，分数在极限时加起来是0：
$$
\sum_{i} p\left(t_{i} \mid \omega\right) S\left(x_{i}, p\left(t \mid t_{i}\right)\right)=0 .
$$
此外，
$$
S\left(x_{i}, p\left(t \mid t_{i}\right)\right)=\log \frac{\omega_{i}}{\mathbb{E}_{p(\omega)}\left(\omega_{i}\right)}+\operatorname{constant}(\omega) .
$$
证明：
$$
S\left(x_{i}, p\left(t \mid t_{i}\right)\right)=\sum_{j} p\left(t_{j} \mid \omega\right) \log \frac{p\left(\omega \mid t_{i}\right)}{p\left(\omega \mid t_{j}\right)}=\log p\left(\omega \mid t_{i}\right)-\sum_{j} p\left(t_{j} \mid \omega\right) \log p\left(\omega \mid t_{j}\right)
$$






