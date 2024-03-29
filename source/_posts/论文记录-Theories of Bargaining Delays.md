---
title: 论文记录-Theories of Bargaining Delays
date: 2022-8-31 21:35:43
tags: game theory
category: 论文
description: 讨价还价延迟理论
---
# Theories of Bargaining Delays

## Abstract

各类争端的解决过程中可能会出现罢工、谈判等延迟现象，而这是有代价的。博弈论说明程序性特征可能会导致拖延，但主要原因可能是各方之间的信息差异。本文描述了几个模型，并与罢工的数据相关。

## Introduction

商业和法律场景广泛存在着讨价还价，而这带来了各方面的有代价的延迟。如果参与者知道最终达成的结果本应能在更早的时候以更低的代价达成，则会觉得遗憾。这是发生冲突的双方在利益分配上的对立所引起的，每个人都相信坚持自己的利益值得付出延迟的代价。

以其他领域的类似的场景为例：

1. 昂贵的经济和政治斗争发生在竞争公司之间的市场份额竞争（价格战）

2. 国家之间的军备竞赛和战争

3. 其他 "霍布斯式 "的背景下的事情

这些例子是缺乏可执行合同和支付手段的讨价还价的变种。同样，动物之间为争夺配偶或猎物而进行的战斗也会选择赢家；其代价是受伤或能量消耗。

然而，在一些物种中，会通过事先进行的锦标赛建立等级制度；在其他物种中，会通过仪式化的战斗降低竞争成本。这些例子表明，一些优势的竞争是信息性的，揭示了参与者的相对实力。在经济背景下，公平分配的行为规范和道德规则模仿了仪式化解决冲突的效果。

前文所提到的个人激励（也就是利益）会促进双方为分蛋糕而付出高昂的代价，这促使人们尝试改善讨价还价的过程——替代性争议解决方案：

1. 雇用第三方调解员来加强沟通并提出妥协方案

2. 由仲裁员对争论进行判断并作出有约束力的决定

针对替代程序的潜力分析，现有研究侧重于如何快速解决谈判的基本障碍。本文提出了来自博弈论的一些结论：

1. 在实践中，讨价还价受到公平观念和强烈情绪的影响。博弈论的表述从这些方面进行了抽象，以找到与每一方不懈的优化行为相一致的结果范围。

2. 一个主要目的是确定允许或要求延迟达成协议的情况。拖延是低效率的一个重要来源，在这些数据中可以观察到的是罢工时间，但它不是唯一的一个。其他的可能性是低效的合同条款，以及当存在互利协议时却未能达成协议。

博弈论所引用的假设是严格的。最大化行为的假设通过以下假设得到了加强：各方都确切地知道管理这一过程的程序规则，并且他们有强大的计算能力。程序的固定性排除了用自然语言的自由流动来传达信息和意图的谈判；相反，语言被限制在提议、接受或拒绝，以及诸如罢工的补充决定。然而，它使人们能够研究替代程序的效果和各方之间的信息差异。计算包括选择自己的策略，以及不太可信的对对方策略的预期。这种均衡的假设——各方的策略都是对对方的最佳反应——是博弈论的一个标志。它不是一个规范性的公理，也不是对经验事实的近似，充其量是模糊的，它是识别快速解决谈判的根本障碍的先决条件。为了获得有用的结果，需要一个更强有力的假设：每一方的策略不仅在最初是最优的，而且在随后的每一个突发事件中也是最优的。这一限制排除了一种策略：其最初的最优性取决于用一种不可思议的威胁来恐吓另一方，从这个意义上说，执行这种威胁并不是最优。

虽然是限制性的，但这些假设使我们能够把讨价还价作为一个策略问题来分析，并以程序规则和有关各方的偏好和信息的数据为条件。这种分析模式与基于公理的规范或道德标准的分析，或基于从行为规律性得出的经验预测的分析形成了对比。我们首先回顾了强调程序性规则影响的模型，然后描述了信息差异的影响。

## Models of Procedural Effects

一个基本的例子：买卖双方针对价值为$v$的商品进行讨价还价，卖家的供应成本是$c$，且$v>c$。

$t$时刻双方就价格$p$达成共识这一结果意味着：这次交易的利润在分蛋糕（总量为$v-c$）时，卖家分到的是$p-c$，买家分到的是$v-p$。一个简单的过程是双方交替报价：以$\Delta$为时间间隔将讨价还价的时间划分为离散的时间段，一方报价后另一方在下一个时间段立即选择同意或者再次报价。

我们考虑所有延迟代价的可能性中的两种极端情况：

1. 代价与延迟成正比，因此卖方净赚$p-c-d_st$，买方净赚$v-p-d_bt$，其中参数$d_s$和$d_b$是卖方和买方在每个延迟时间段里产生的成本。

2. 它们代表了投资收益中放弃的利息收益，即卖方净赚$[p-c]\delta_s^t$，买方净赚$[v-p]\delta_b^t$。参数$\delta_s$和$\delta_b$是反映利率$r_s$和$r_b$的贴现因子。例如：$\delta_s=e^{-r_s\Delta}$。

法律和仲裁的背景增加了一个特点，即在某个时候，如果双方仍有分歧，则由法官对争议进行裁决。

它规定了一个明确的程序，并且偏好表现出对解决的不耐烦，在这个意义上，延迟是昂贵的。这个例子的一个主要预测是，协议立即发生，均衡价格是唯一的。唯一性需要详细的证明，但由此产生的没有延迟是合理的：延迟的协议会被双方预料到，因此在任何较早的时间，提议的一方可以提供对双方有利的条款。例如，卖方可以提前提出一个较高的价格，而买方宁愿接受这个价格，也不愿承担等待以后预期的较低价格的成本。

均衡价格取决于谁第一个报价。如果卖家报价$p_s$，买家报价$p_b$，则$p_s$是买家为了在下一轮能报价$p_b$的不能拒绝的最高价格，而$p_b$是卖家不能拒绝的最低价格并在下一轮中报价$p_s$。

在第2种情况下（延迟成本等价于投资收益中放弃的利息收益），这两个价格由下面的公式决定：

$$
v-p_s=[v-p_b]\delta_b\\
p_b-c=[p_s-c]\delta_s
$$

这个公式粗浅的理解如下：左边是最初报价就达成共识的收益，右边是讨价还价后用贴现因子折算之后的收益，如果上述等式成立，则说明，如果第一轮是卖家报价，则该报价所对应的买家收益，与之后买家自己报价折算后的收益相等；如果第一轮是买家报价，则该报价所对应的卖家收益，也与之后卖家自己报价折算后的收益相等。由此达成均衡。在这种情况下，当利率相近且报价之间的时间间隔较小时，任何一种价格都能提供几乎均匀的分红。

而在第一种情况下，则会产生一种不对称的分蛋糕结果：成本较小的一方如果能先报价，则会得到整个蛋糕，反之，则会得到除了能抵消对方成本以外的剩下蛋糕。如果某一方有替代选项，例如买家能去其他地方以价格$p$购买该商品，则当卖家先报价时，预测价格是$p$和$p_s$中较小的一个。

在这个例子中，拖延显然是没有必要的。如果各方能预见到最终的分裂，而且程序允许更早的解决，那么他们就有动力迅速决定事情。类似的主张也适用于Mnookin和Kornhauser(7)研究的 "法律阴影下的讨价还价"。在法律索赔的审前讨价还价中，如果法院对纠纷的最终解决方式事先是明确的，那么当事人就有强烈的动机去和解。Bulow和Rogoff(8)提供了这些观点的另一个应用，即银行和主权国家之间关于重组债务合同的讨价还价。

然而，这个命题需要限定，为了说明这一点，我们描述了一个支持多个均衡的程序性修改，从而使得延迟是必要的。假设时间是连续的，且参数$\Delta$和$\Delta'$表示两个固定的时间段。每一方可以在任意时间$t\ge0$提出一个价格，只要他在对方上一轮报价后至少等待了$\Delta$，且在他自己上一轮报价后至少等待了$\Delta'$（如果自己有上一轮报价的话）。当一方重复另一方的报价或他们同时提出相同的价格时，就达成了协议。这种修改后的程序有一个优点，即它减轻了对报价时间的限制。然而，作为均衡结果，它允许在零时和更晚的时间有广泛的价格；因此，延迟是可能的，但不是必须的。拖延的近因是立即达成协议的多样性，允许对任何试图提前解决的人附加 "烙印"。

假设当他们都保持沉默直到同时报价$p^0$时，一个价格为$p^0$的延迟协议出现，接下来的内容完全没有看懂，大致意思是说，这个修改了程序的讨价还价博弈由于有着多重均衡，而使得博弈双方总是无法达成共识，从而导致了延迟的出现。这种延迟是所有允许多重均衡的博弈的特点。事实上，如果同时提出的建议被排除在外，比如说通过忽略其中一个建议，那么同样存在着一个唯一的均衡，没有延迟。

多重性也会在其他博弈程序中出现。Fernandez和Glazer[10]分析了以工会和公司之间的讨价还价为例的在固定间隔内交替报价的程序，新增的一个特点是在每个没有就新合同达成协议的时期，工会可以选择罢工或者按前一个合同规定的工资工作[11]。工会可利用的这种报复性选择的多重性，允许有一系列的即时工资协议；因此，就像前面的例子一样，仪式性的延迟也是可能的。

第二个条件是，各方必须能够预见最终的协议。当任何一方拥有关于该过程的某些方面的私人信息时，这一点就失效了。例如，在法律背景下，一方可能知道受制于证据或随后在审判证词中披露的信息。其他的可能性是，程序是模糊的，或者任何一方对另一方的偏好不确定。我们用后者来说明私人信息的影响。

## Models of Informational Effects

关于信息差异如何导致延迟，有三个主要例子被研究。根据参考文献[12]，我们把它们称为损耗、筛选和信号的模型。

### Attrition 损耗

损耗模型假设偏好符合情况1，但是每一方都不确定其他人的延迟成本，只假设其符合一个确定的概率分布。虽然是为了描述动物之间为生存或繁殖而竞争而开发的，但损耗模型也适用于讨价还价。因为它的成本是相互的，延迟类似于一场战斗，以确定较强的一方（成本较小的一方），然后像上面的基本模型一样要求得到整个蛋糕。损耗模型恰当地描述了为生存或市场份额而竞争的公司之间的军备竞赛和价格战，以及其他隐性议价的情况。它们捕捉到了回顾性的遗憾感：如果一开始就确定较强的一方，那么战斗的浪费是可以避免的；但悲剧的是，战斗可能是对实力的唯一可信的测试。双方的动机前瞻性地说明了一类更广泛的声誉模式：每一方的持续斗争都是对可能更强大的声誉的投资，如果对方先投降，就能获得有利的回报。

损耗模型似乎与工资和价格的明确讨价还价不太相关，因为它们依赖于影响利润的延迟成本的信息隐私，而且它们对赢家通吃结果的预测似乎不现实。

### Screening 筛选

筛选模型通常假设偏好符合情况2，在最简单的版本中，卖方对买方的估价是不确定的。尽管$v$对买方来说是已知的，但卖方认为它是一个符合某一公开的概率分布的随机变量。我们进一步简化，假设卖方在离散时间$t=0,\Delta,2\Delta,...$时进行报价。

在这个版本中，卖方使用 "撇脂 "策略对买方的可能类型进行排序。也就是说，他提供连续较低的价格，直到买方接受。这种策略利用了高价值买家对交易的不耐烦。买家等待低于其估值的价格，然后在进一步拖延的成本超过等待下一个更低价格的优势时接受。预测到买方的行为，卖方设计了一个最佳的报价序列，均衡基本上是唯一的，假设卖方的成本严格小于买方的所有可能的值，所以交易的收益肯定是正的。

然而，这个模型对延迟的预测是短暂的。随着报价之间的间隔$\Delta$的缩短，最大的延迟缩减为零，卖方的报价下降到买方的最小可能值。只要均衡具有上述唯一均衡的一些一般特征，这一引人注目的属性就会在更一般的版本（包括交替报价）中持续存在。因此，只有在报价不频繁的情况下，重大延迟才是必要的。

关于报价频率的理论（除了简单的物理限制和 "继续尝试 "的诱因）必须与重要的声誉方面相抗衡。在罢工期间，公司拒绝工会提出的工资会产生一种激励，即迅速提出更低的工资以缩短罢工时间。但是，工会可以预见到科斯属性：准备好的提议经常鼓励公司预见到并等待更低的工资提议，这最终会产生快速的协议，但条件对工会是不利的。通过在提供更低的工资之前忍受相当长的罢工时间，使公司为拒绝的后果付出代价，这是工会获得可观份额的力量的来源。因此，筛选模型有一个微妙的含义：建立一个强硬的声誉（被理解为维持成员的士气以在相当长的时间内不妥协地维持工资要求的能力）是一个成功战略的主要特征。建立声誉可能涉及到与一家公司或一个行业中的许多公司在连续的合同谈判中反复讨价还价。在这个意义上，延迟是由于买方价值的不确定性而加剧的，这就证明了卖方的撇脂策略，以及建立声誉的首要动机是不经常修改报价。

### Signaling 信号

与上述声誉解释一样，信号模型提供了报价之间的重要间隔的解释。然而，它通过解决买方的动机来证明低价是合理的，从而避免了科斯产权理论。该模型中允许随时报价，但必须双方轮流，一方报价后另一方要等一段时间。假设买方的价值是私有的。

现有一种信号装置，其运作方式如下：在收到卖方提出的价格后，买方可以在间隔时间$\Delta$后接受，或者等待更长的时间，以表明他的价值很小，然后提出一个更低的价格。为了使这种信号可信，他必须等待足够长的时间来反驳任何关于他的价值可能很高的猜想。因此，他等待的时间和他提出的价格必须是这样的，如果他的价值更高，那么这样做是无利可图的。只要强制区间$\Delta$足够短，弱技术假设就意味着有这种形式的唯一均衡。特别是，如果卖方的价值足够高，他最初提出一个被买方接受的价格，否则，他通过等待一个适当长的时间间隔来提出他的价值（因为现在他的价值已经被可信地发出信号），即最初描述的没有不确定性的基本模型所预测的价格。

在双方都有私人信息的情况下，也会得到类似的结果。更没有耐心的一方，比如说卖方，在提出最初的建议之前，会等待足够长的时间来发出可信的成本信号；然后，如果买方的价值非常高，他会迅速接受，否则会等待足够长的时间来发出可信的价值信号，然后提出反建议，卖方会迅速接受。根据这种情况，双方通常在没有私人信息的情况下就预测的价格达成一致：与筛选模型不同，信息差异对商定的条款影响不大。延迟的作用仅仅是可信地传达彼此的私人信息。后悔的因素体现在事后意识到早期对卖方的成本和买方的价值进行可信的核查可以避免延迟的成本。

根据这一设想，拖延是信息不对称的必然结果。特别是，最多只有两个正式报价，其余的都是多余的拖延，例如在工资谈判中，公司发出的信号，即它无法承担高工资。从严酷的形式来看，这种观点违反了偶然的观察，但一些特征，如可变的反应间隔和稀少的认真报价是现实的。此外，工资是公平的，因为在没有信息差异的情况下，工资是相同的。筛选模型有很大的不同，因为它们预测工资和延迟主要取决于程序性参数，如报价的频率，反过来又取决于对士气或声誉的辅助考虑；此外，如果报价频繁，那么结果就会对拥有优势信息的一方非常有利。

这三种延迟模式代表着极端情况，其中有可能出现各种混合情况。例如，如果当事人的延迟成本是(i)和(ii)的混合物，那么结果也是损耗和信号结果的混合物：与纯粹的损耗模型相比，延迟可能更短，分割更公平。新的特征也出现了。买方的最高值意味着立即接受，但最低值也会导致买方终止谈判，即使交易的收益是正面的：可信的信号传递太昂贵了。如果卖方的成本和买方的价值是相关的或有因果关系的，就会出现更多的可能性；特别是，即使双方都知道收益必须是正的，也可能阻止收益的实现。

## Strike Data

损耗模型预测，平均工资结算与罢工持续时间无关，加拿大的数据就是如此，尽管美国的数据显示工资随着持续时间的增加而有所下降。他们还预测，解决率（每天在仍未解决的问题中解决的百分比）会下降，这与数据基本一致。如果只有公司有私人信息，筛选和信号模型预测工资会随着时间的推移而下降，如前所述，这在美国的数据中也有发生，但它们在下降的速度上有所不同。筛选模型的合理规格可以预测适当幅度的结算率（每天2％或3％），如果包括非罢工放缓（如下所述），信号模型也可以。这些模型预测的结算率大多随着时间的推移而增加，但这与总体数据中观察到的结算率下降并不矛盾：因为不同的工会-公司对可以有不同的结算率，而那些有高结算率的公司倾向于早期结算，总体结算率仍然可以在很长一段时间内下降。

信号模型可以适应对其他特征的考虑，如工会在放缓或罢工之间的战术选择，以向公司施压。事实上，罢工和停工在合同到期和结算之间所占的平均时间不到一半；另外，大多数合同的续签都会发生延误，但其中只有不到三分之一涉及罢工，而罢工通常是迅速启动的。信号模型很好地预测，只有在旧工资较低的情况下，工会才会在最初选择罢工。值得注意的是，合理的假设意味着减速和罢工以类似的速度传递信息，以被延迟成本耗尽的部分来衡量；因此，当旧工资不是太低时，减速是相对有效的，对工会有吸引力。

还需要进一步的工作来确定每个模型在整体上对数据的适合程度。一些孤立的事实支持了这些模型的一般假设，即战略行为，因信息不对称而变得复杂，是很重要的。这些模型预测，在纽约，工人在8周后有资格获得失业补偿，这鼓励了较高的早期结算率和较低的后期结算率，事实上也是如此。他们还预测，魁北克省禁止企业雇用替代工人的法律增加了平均工资和罢工持续时间，事实上，平均持续时间的估计增长超过了50%。这种影响在筛选模型中是最明显的，在这种情况下，排除替代工人会增强工会根据企业的盈利能力进行歧视的权力，而不受来自非工会工人的竞争的阻碍。另一方面，在实践中，延迟成本似乎比这些模型预测的要低。在美国，只有大约15%的劳动合同谈判涉及罢工（在加拿大为22%），与持续3年的合同相比，中值持续时间通常为3周。同样地，约有10%的法律索赔进入审判阶段（儿童监护权等问题的比率更高）。在接受强制性约束性仲裁的政府工作人员的合同谈判中，约有三分之一涉及仲裁听证，但这些谈判的成本比罢工或审判要低。对这些现象的一个可能的解释是，只有少数争端受到信息不对称的困扰。

## Discussion

这里描述的模型对昂贵的讨价还价延迟的根本原因提出了建议。没有信息差异的模型表明，拖延不是固有的。合理的程序能够避免任何拖延的必要性。然而，有信息差异的三个模型表明，拖延可能是可能的或必要的。尽管它们依赖于偏好的不同结构特征，但损耗和信号模型都有一个共同的预测，即延迟主要是一种激发信息的手段。拖延的成本性确保了其作为信号的可信度，所以如果没有其他的验证手段，那么各方可能会依赖它。另一方面，筛选模型则提出了允许操纵延迟的程序。频繁的报价使延迟最小化，这在社会上是有效的，但却使条件对信息较差的一方有偏见。

这些不同类型和数量的延迟的可能性也反映在对任何议价过程的任何均衡所隐含的基本激励兼容条件的研究中。这些条件与程序性规则无关，但它们对信息不对称很敏感。一个基本的含义是，关于贸易收益是否为正数的相互不确定性需要一些无效率，要么是延迟，要么是谈判的破裂。在满足这些条件的方式中，发现了大量的可能性，每一种都会对各方获得的相对优势、延迟成本和结果的整体效率产生影响。一个实际的建议是，通过建立（在信息差异出现之前）长期合同来调节争端，从而避免这些条件的咬合。另外，单方支付可以补偿预期的不利因素；例如，在一个筛选模型中，报价之间的短间隔对卖方不利，但可以减少延迟，因此作为补偿，买方可以向卖方支付费用以加快进程。

这些评论并不意味着所提到的延迟来源是详尽的。由于情感和认知反应，如不信任或归因于恶意，造成的延迟是很常见的。即使在匿名的情况下，每一方都可能不愿意放弃他应得的、期望的或承诺给选民的份额。即使对于 "超理性 "的一方，也有相互竞争的假说。其中一个重要的假设是，受雇于各方的代理人可能有动机去拖延（如果他们有报酬去谈判）或避免达成协议（如果这将对他们的未来产生不利影响）。后者的一个可能的例子是，在职管理层或董事有动机阻挠敌意收购或兼并要约，或阻挠股东诉讼的解决。一个常见的观点是，在问题成熟时才会结束谈判。就上述模型而言，成熟性可以被解释为进一步拖延或获取或推断新信息的成本增加；但也可能是拖延使后果或偏好得到澄清。在法律背景下的一个相关例子是，延迟提供了进行发现或辅助性法庭测试的机会，如上诉。

最后一段我也没理解，大致意思应该是说关于讨价还价博弈中的延迟分析依赖于各方的理性，而这限制了其应用。它对程序和偏好的详细说明的依赖也带来了应用上的限制性。接下来举了几个例子说明这种限制。