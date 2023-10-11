---
title: 论文记录-Online Organizing Large-scale Heterogeneous Tasks and Multi-skilled Participants in Mobile Crowdsensing
date: 2021-05-26 13:26:13
tags:
    - crowdsourcing
    - 审稿意见
categories: 论文
description: 是TMC的审稿意见
---
# Online Organizing Large-scale Heterogeneous Tasks and Multi-skilled Participants in Mobile Crowdsensing

是TMC的审稿。

<!--more-->

## Abstract

目的：在线收集大规模异构任务和多技能参与者

方法：层次树和时间序列

内容：计算模式、树的创建方法和任务分配策略

## 1. Introduction

场景：多技能参与者的在线众包平台上实时分配大规模异构任务

面临的问题：

1. 丰富的技能类型。技能类型太多，甚至需要一些领域的专业知识才能合理分类，平台应该如何处理。
2. 不规则的任务时空要求。
3. 多任务时空重叠。针对相同内容的任务，平台可以去除冗余；不同内容的任务，平台分给同一个workers可以减少其移动成本。
4. 参与者和任务的动态性。任务和参与者随时到达或者离开平台。考虑参与者接了任务却没完成就离线的情况。任务一旦被分配了就不能被删除。
5. 多技能参与者。
6. 参与感知和机会感知。两种类型的参与者都应该能参与进来。
7. 点推荐和轨迹推荐。两种任务推荐算法共存。

现有文献主要在线多任务分配问题的不同方面提出了具体解决方案：

1. 针对即时任务分配问题，提出了基于贪婪的任务分配算法
2. 针对时空异构任务分配，提出了分布式参与者选择框架
3. 在任务动态异构的场景，考虑任务成本和能力等约束，选择能满足任务需求的最少数量的参与者
4. 自组织移动众包框架，任务提供者可以主动、实时地向周围的移动参与者发布任务，从而快速获取高质量的传感数据。
5. 两阶段任务分配框架，将机会感知和参与感知的优势结合起来。
6. 多时空约束（包括任务位置、时间窗口、参与者轨迹等）的任务分配问题
7. 不同预算的多任务，满足信息质量需求，考虑不同参与者的激励需求、传感能力和移动性。

上述解决方案的问题：没能同时解决这些问题

存在这一问题的原因：数据结构过于简单

本文方案：

1. 原子化时间和空间——处理不规则时空问题，简化多任务时空重叠问题
2. 将异构任务划分为原子任务，使用层次树进行组织——包括技能层、时间层、空间层和原子化任务层（叶节点），技能是需求驱动的，并不提前列出所有技能。技能层、时间层和空间层是可扩展、自适应的。
3. 使用时间序列组织动态参与者，将空间、时间和技能约束等封装，根据时间限制加入队列，参与感知和机会感知都可以，点推荐和轨迹推荐共存。
4. 综合上述，得到在线大规模异构任务分配算法。

本文贡献：

1. 第一个研究这个的
2. 两个数据结构结合
3. 实验表明了有效性

本文组织结构：略

## 2. Related Work

单任务场景：

多任务场景：

数据结构相关：

## 3. Methodology

平台架构：

1. 资源层：包括任务池和参与者池。任务发布者完善任务内容和技能类型。
2. 数据结构和算法层：包括原子化任务、任务层次树、参与者时间序列队列和任务分配算法。首先，根据任务的技能、时间和空间对任务进行原子化处理；其次把原子化的任务根据技能时间和空间属性插入层次树中；同时封装所有参与者的属性并基于时间属性更新到时间序列队列中；最后根据时间序列队列和层次树进行匹配，输出结果。
3. 输出层：任务成功执行，则返回传感数据；任务失败，如果没到ddl，就加入任务池变成新的任务；如果到了ddl，就删了并返回失败结果。

### 3.1 Atomized tasks

把任务按技能、空间、时间进行原子化处理，技能的原子化是按任务发布者所确定的技能数据库来做，空间和时间原子化是考虑具体场景，比如时间按一分钟划分、空间按一平米划分等。具体来说可以参考下图的任务划分。

![image-20210527143503549](https://i.loli.net/2021/05/27/iaSz6o5dOTYZkVl.png)

### 3.2 Task hierarchy tree

#### 3.2.1 Create task hierarchy tree

任务的层次树有4层：技能层、时间层、空间层、原子任务层（叶节点）

技能层并不提前列好所有技能，而是需求驱动

前面那个图中原子任务们对应的层次树生成过程如下图所示：

![image-20210527143832471](https://i.loli.net/2021/05/27/6suQJanpBrbPUxH.png)

具体来说，就是从根节点出发，在第一层找对应的技能节点，接着找对应的时间和空间节点，最后把这个任务放到对应的叶节点上。这中间如果没查到节点，就直接新建。

#### 3.2.2 Creation methods of the task hierarchy tree

层次树两种建立方式：空间优先和时间优先。具体来说如下图所示：

![image-20210527144407143](https://i.loli.net/2021/05/27/kKPF9WLUujsOrSM.png)

#### 3.2.3 Computing pattern of task allocation

两种计算方式：串行和并行，串行效率低，并行要考虑任务分配的互斥锁。

通过多核处理器实现并行：按技能将分层树分成多个子树，这样多个参与者可以遍历不同的子树，实现并行计算。

具体来说，选参与者的时候会避免技能重复，比如选好第一参与者之后，第二个参与者若有相同的技能，就会跳过去看第三个参与者。这样最后选出来的参与者就都是技能不重复的，每人得到一颗子树及其原子任务们。

分给同一个参与者的多个子树要进行合并，即：把相同的时间和空间节点合并成一个节点。具体如下图：

![image-20210527150457291](https://i.loli.net/2021/05/27/Fy4rhoamwisuq2P.png)

### 3.3 Participant time-series queue

参与者有7个属性：参与者ID、当前时间、当前位置、目的地、到达目的地的容忍时间、移动速度、技能向量。

根据当前时间将参与者依次加入到时间序列队列中。更新时间序列的过程如下图：

![image-20210601103602834](https://i.loli.net/2021/06/01/vUGhYEMTJwiqNCr.png)

具体来说，就是根据`current time`把参与者排成队列，新来的插进去，分配好任务后把`current time`改成执行任务结束后的时间，无任务的节点从序列中删去。对于点推荐的任务，就把它分给任务要求时间与`current time`完全一致的参与者；而对于轨迹推荐的任务，就把它分给时间序列队列中的所有参与者，直到队列空了。

这个图和论文里的文字描述看起来没对应上，主要存在以下问题：

1. 对这个图的文字描述里写的是参与者$tf_6$和$tf_7$，而图里出现的是$tf_6$和$tf_5$，这肯定有一个错了；
2. 文字描述里是先把$tf_6$的`current time`调到`10:08`，然后再因为$tf_1$没有任务而删除它，图里这俩过程反过来了；
3. 图中第4行和第6行出现了$U_6$和$U_7$两个符号是什么含义，与其他参与者有什么区别？这需要在文字说明部分加以解释。

### 3.4 Task allocation algorithm

任务分配约束：

1. 参与者技能是否满足任务对技能的需求；
2. 参与者是否能及时赶到任务执行地点；
3. 执行任务是否会导致参与者无法准时到达自己的目的地；
4. 任务地点是否会让参与者离自己的目的地越来越远。

任务分配四步：技能匹配、时空匹配、分配策略、参与者更新。这四步如图所示：

![image-20210601104812332](https://i.loli.net/2021/06/01/WQcLDaMinhOsIk9.png)

具体来说，首先判定一番，发现$l_4$会让参与者无法按时到目的地，所以排除；$l_1$会让参与者离目的地更远（从5变成5.4），所以排除；$l_2$和$l_3$都可，且此时这俩位置都只有1个任务，在短距离优先的情况下就分配$l_2$了。

任务树采用深度优先遍历的方式。

### 3.5 Method summary

这部分总结了一下，没啥能说的。

## 4. Evaluation

### 4.1 Data set

两个前提：

1. 参与者随时能离线，即便他接了任务没做（这种情况下平台进行任务再分配）；
2. 任务仅能由发布者删除，任务一旦被分配则不能被删除。

任务描述：`Task={task content, required skill, execution area, execution time period, upload time, deletion time}`

参与者描述：`Participant={participant ID, current time, current location, destination, tolerable time to reach the destination, moving speed, skill vector, and offline time}`

数据集：将`10KM*10KM`区域均匀划分为`100*100`的方格，将`60min`均匀分为`60`个时刻，即：时空最小单位是$0.01KM^2/min$

所有参与者和任务根据以下要求随机生成：

1. 任务执行区域是具有任何形状的连续区域，其大小不超过$1KM^2$;
2. 任务执行时间是任意长度的连续时间段，长度不超过5分钟；
3. 任务删除时间比任务刷新时间长；
4. 参与者能容忍的到达目的地时间会根据从当前位置到目的地位置的最短时间而随机增加；
5. 参与者最高移动速度不超过`1KM/min`，最低速度不低于`0.5KM/min`；（这个速度设置不太合理，没考虑不同交通工具等）
6. 参与者至少1个技能，至多5个技能；
7. 参与者离线时间比当前时间晚；
8. 假设有3种参与者分布：均匀分布、高斯分布（均值50，标准差18）、Check-in分布。任务分布采用均匀分布。

由此得到7组数据。（这里的check-in分布没查到，不知道是什么含义）

### 4.2 Comparison aspects and evaluation indicators

比较3方面：

1. 计算模式：串行和并行
2. 任务树建立方式：空间优先和时间优先
3. 任务分配策略：无策略和短距离优先原则

关注4个评价指标：

1. 算法执行时间
2. 任务和参与者匹配率
3. 参与者行动距离
4. 冗余任务移除

### 4.3 Experiment Results

比较了：

1. 不同任务数、参与者数、任务内容、技能类型条件下的效果
2. 参与感知和机会感知、点推荐和轨迹推荐的效果
3. 不同参与者分布下的效果

实验图表数据就不列了，都是进行数据对比分析。

总结如下：

1. 并行计算能节省算法运行时间；
2. 时间优先的层次树建立方法比空间优先的要更能增加任务和参与者的匹配率；
3. 增加短距离优先原则能减少参与者的行动距离；
4. 任务内容重复率越高，冗余任务去除越多（这不是废话吗）。

### 4.4 Analysis and Discussion

前面所说的1和3都很显然，这里分析2的原因。

时间优先的层次树在时间节点那一层是单调增排序的，也就是说时间较早的会先被检索到进而匹配，这样参与者在匹配完该任务后还能继续匹配时间更晚一些的任务；而在空间优先的层次树中，参与者可能会先被匹配到时间较晚的任务，接着其`current time`就被刷新到更晚，从而无法匹配时间较早的任务了。

## 5. Conclusion and future work

在线收集大规模异构任务和多技能参与者可以使任务和参与者实时共享。然而，这将使任务分配非常困难。我们总结了在线收集对任务分配平台的七个要求。它们包括丰富的技能类型、任务的不规则时空要求、多任务的时空重叠、任务和参与者的动力学、多技能参与者、参与式感知和机会主义感知、点推荐和轨迹推荐。为了满足这七个要求，我们设计了一种基于层次树的任务组织方法和一种基于时间序列队列的参与组织方法。基于任务层次树和参与时间序列队列，从计算模式、树生成方法和任务分配策略三个方面研究了任务分配问题。在计算模式上，我们比较了串行计算和并行计算。在树的创建方法中，我们首先比较空间和时间。在任务分配策略中，我们增加了短距离优先原则。最后，对算法运行时间、任务参与者匹配率、参与者行程距离和去除冗余任务四个指标进行了详细的实验评价。实验结果表明，基于任务层次树和参与时间序列队列的任务分配算法能够有效地满足这七个要求。在实验环境中，与串行计算相比，并行计算可以平均减少算法运行时间66.00% 以上。与空间优先相比，时间优先平均可使任务参与者匹配率提高13.00% 以上。增加短距离优先原则可以平均减少参与者出行距离4.00%  以上。

今后还有许多问题需要解决。首先，在进行时空匹配时，我们假设参与者的旅行时间可以被精确估计。然而，即使使用最好的商业地图，也不能准确地估计旅行时间。在这种情况下，如何实现任务分配？我们的想法是，我们可以返回任务的一个周期时间和一个区域，而不是一个时空点。其次，当前树的划分仅根据技能层进行。是否有可能在时间和空间层进一步划分树？我们认为这是可行的。在计算资源充足的情况下，应进一步减少算法运行时间，但效果可能不明显。因为在技能层分割后，树的尺寸并不大。第三，任务不仅具有技能、时间、空间三个属性，而且还具有主题属性，这三个属性对应着参与者的偏好。任务层次树是否可以添加主题属性并考虑参与者的首选项？我们认为这是可行的。在添加了主题属性之后，树的层数从4层改为5层。此外，考虑参与者的偏好会导致任务与参与者之间的概率匹配问题。任务分配问题的复杂性将进一步增加。

## 审稿意见部分

本文认为当前针对在线多任务分配问题的诸多解决方案无法同时解决不同的问题，其原因在于它们采用的数据结构过于简单，针对这一点本文提出了基于层次树的任务组织模式和基于时间序列队列的参与者组织模式，并在此基础上设计了任务分配算法。针对不同的计算模式、任务树建立方式和任务分配策略，本文采用随机生成的数据集进行了仿真实验分析，根据算法运行时间、任务参与者匹配率、参与者行程距离和去除冗余任务这四个指标对算法性能进行评估，从而得出结论：并行计算能够减少算法运行时间；时间优先的层次树能提高任务和参与者的匹配率；增加短距离优先原则可以减少参与者出行距离。

strength:

1. 作者清晰地阐述了本文的主要思想，完成了研究工作，包括详细的性能分析；
2. 本文从数据结构的角度入手分析在线多任务分配问题，并提出了基于层次树和时间序列队列的任务分配算法，有一定创新性；
3. 本文对算法思想的描述十分详细，图表清晰地解释了算法过程和实验数据，有利于读者阅读论文；
4. 本文考虑了点推荐和轨迹推荐的任务分配模式以及参与感知和机会感知的参与者行为模式，更贴近真实场景。

weakness:

1. 图10中存在下列问题：
   1. 对这个图的文字描述里写的是参与者$tf_6$和$tf_7$，而图里出现的是$tf_6$和$tf_5$，这两处表达需要统一；
   2. 文字描述里是先把$tf_6$的`current time`调到`10:08`，然后再因为$tf_1$没有任务而删除它，图里这俩过程反过来了；
   3. 图中第4行和第6行出现了$U_6$和$U_7$两个符号是什么含义，与其他参与者有什么区别？这需要在文字说明部分加以解释。
2. 实验部分中提到的check-in分布是否可以增加具体的介绍？
3. 本文实验部分假设参与者最高移动速度不超过`1KM/min`，最低速度不低于`0.5KM/min`，这个速度设置主要是汽车的速度范围，没有考虑其他交通方式如步行、骑行等，如果可以放宽速度限制范围，会更合理一些；
4. 本文实验部分如果可以增加与现有算法的性能简要对比，会更有说服力。

英文版本：

This paper argues that many current solutions to online multi-task  assignment problems can not solve different problems at the same time.  And the reason is that the data structures they used are too simple to solve these problems. This paper proposes a task organization model based  on hierarchical tree and a participant organization model based on time series queue, and designs a task allocation algorithm. According to  different computing patterns, tree creation methods and task allocation strategy, this paper uses randomly generated data set to carry out  simulation experiment analysis. The performance of the algorithm is evaluated according to four indexes: algorithm running time, task participant matching rate, participant travel distance and redundant tasks removed.  It is concluded that  parallel computing can reduce the running time of the algorithm;  time-first hierarchical tree can improve the matching rate of tasks and  participants, and short-distance-first principle can reduce the travel  distance of participants.

strength:

1. The main idea of this paper is described clearly and the research work is complete, containing detailed performance analysis.
2. This paper analyzes the problem of online multi-task assignment from the perspective of data structure, and proposes a task assignment algorithm based on hierarchical tree and time series queue, which is innovative to some extent.
3. In this paper, the algorithm idea is described in detail, and the figures clearly explain the algorithm process and experimental data, which are very helpful for readers to understand this paper.
4. This paper considers different participant distributions, different sensing methods(i.e. participatory sensing and opportunistic sensing), and different recommendation methods(i.e. point recommendation and trajectory recommendation), which is closer to the real scene.

weakness:

1. In Fig. 10,  participants tf_6 and ​tf_7 are written in the corresponding text description(Page 8, line 54, right column), while ​tf_6 and tf_5 appear in the figure. These two  expressions need to be unified.
2. In the text  description, change the current time of tf_6 to 10:08 first, then delete tf_1  from time-series queue. But the two processes are reversed in Fig.10. Picture content and text description should be unified.
3. What do the u_6 and u_7  in Fig. 10 mean and how are they different from the other participants? This needs to be explained in the paper.
4. What is the specific form of the check-in distribution mentioned in Section 4?
5. The experimental part of this paper assumes that "The maximum moving speed of participants does not exceed 1KM/min, and the minimum moving speed is not lower than 0.5KM/min." This speed limit is  mainly the speed range of cars, did not consider other modes of  transport such as walking, cycling. If the speed limit can be relaxed,  it will be more reasonable.
6. The evaluation part will be more convincing if it can be compared with the performance of existing algorithms.
