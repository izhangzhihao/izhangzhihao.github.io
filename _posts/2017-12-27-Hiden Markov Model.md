---
layout: post
title: 隐马尔可夫模型
categories: 机器学习
description: 隐马尔可夫模型
keywords: HMM, Hiden Markov Model
math: true
---

## 信念网络（BN：Belief Network）
贝叶斯网络(Bayesian network)，又称信念网络(Belief Network)，或有向无环图模型(directed acyclic graphical model)，是一种概率图模型，于1985年由Judea Pearl首先提出。它是一种模拟人类推理过程中因果关系的不确定性处理模型，其网络拓朴结构是一个有向无环图(DAG)。我们将有因果关系（或非条件独立）的变量或命题用箭头来连接（换言之，连接两个节点的箭头代表此两个随机变量是具有因果关系，或非条件独立）。若两个节点间以一个单箭头连接在一起，表示其中一个节点是“因(parents)”，另一个是“果(children)”，两节点就会产生一个条件概率值。 例如，假设节点E直接影响到节点H，即E→H，则用从E指向H的箭头建立结点E到结点H的有向弧(E,H)，权值(即连接强度)用条件概率P(H|E)来表示。把某个研究系统中涉及的随机变量，根据是否条件独立绘制在一个有向图中，就形成了贝叶斯网络。

$ p(x_1, ..., x_D) = \prod_{i=1}^D p(x_i|pa(x_i)) \\ $
其中：$ pa(x) = x的父母(parents) $

## 隐马尔科夫模型（Hidden Markov Model）

马尔科夫模型其实是一种特殊的贝叶斯网络，即一条链状的贝叶斯网。隐马尔科夫模型（Hidden Markov Model）经常被用在时间序列（例如一段时间内的声音信号，运动物体的位置等你所感兴趣的物理量）的建模与分析。

它有三个要素:

1. 可见随机变量：用来描述你所感兴趣的物理量，随时间变化
2. 隐含的状态变量：每个时间点的物理量背后都对应的不可见的状态量
3. 变量间的关系：用概率的方法（通常是概率密度函数）描述以下三个关系或变量：
    * 初始状态量
    * 当前的隐含状态量与下一个隐含状态量间关系（此处还用到马尔科夫假设：***当前隐含状态只取决于前一个隐含状态***）
    * 当前的隐含状态量与可见随机量间关系

相关算法主要有三类：

1. 已知隐含状态数量、转换概率、可见状态链，求隐含状态。

    知道骰子有几种（隐含状态数量），每种骰子是什么（转换概率），根据掷骰子掷出的结果（可见状态链），我想知道每次掷出来的都是哪种骰子（隐含状态链）。

    [Viterbi Algorithm](https://en.wikipedia.org/wiki/Viterbi_algorithm):
    给定隐马尔科夫模型状态空间$ S $，共有 k 个状态，初始状态 $ i $ 的概率为$ \pi_i $，从状态$ i $ 到状态 $ j $的转移概率为 $ a_{ij} $。令观察到的输出为$ y_1 ,\cdots, y_T $，产生观察结果的最有可能状态序列$ x_1,\cdots,x_T $由递推关系给出：
    $ V_{1,k} = P(y_i | k) \bullet \pi_k $
    $ V_{t,k} = P(y_t | k) \bullet \max_{x∈S}(a_{x,k} \bullet V_{t-1,x}) $

    此处 $ V_{t,k} $ 是前 $ t $ 个最终状态为 $ k $ 的观测结果最有可能对应的状态序列的概率。 通过保存向后指针记住在第二个等式中用到的状态$ x $可以获得维特比路径。声明一个函数 $ \mathrm {Ptr} (k,t)$ ，它返回若 $ t>1 $ 时计算 $ V_{t,k} $ 用到的 $ x $ 值 或若 $ t=1 $ 时的 $ k $ .

2. 已知隐含状态数量、转换概率、可见状态链，求出现当前可见状态链的可能性。

    还是知道骰子有几种（隐含状态数量），每种骰子是什么（转换概率），根据掷骰子掷出的结果（可见状态链），我想知道掷出这个结果的概率。

    [Forward Algorithm](https://en.wikipedia.org/wiki/Forward_algorithm):
    $ \alpha_i(t)=P(y_1,y_2,\cdots, y_t, q_{t}=i\ |\ \lambda) $
    我们的新记号 $\alpha_i(t)$ 给出了当状态 $q_{t}=i$时，它与到时刻 $t$ 为止所有的观测值的联合分布的概率。
    $\alpha_i(1)=P(y_1, q_{1}=i)=P(y_1|q_1=i)\cdot P(q_1)=b_i(y_1)\cdot \pi(q_1)$
    $$\begin{split} \alpha_j(2)=P(y_1,y_2, q_{2}=j)&=\sum_{i=1}^kP(y_1,y_2,q_1=i,q_2=j)\\ &=\sum_{i=1}^kP(y_2|q_2=j)\cdot P(q_2=j|q_1=i)\cdot P(y_1,q_1=i)\\ &=\sum_{i=1}^kP(y_2|q_2=j)\cdot P(q_2=j|q_1=i)\cdot \alpha_i(1)\\ &=P(y_2|q_2=j)\sum_{i=1}^kP(q_2=j|q_1=i)\cdot \alpha_i(1)\\ &=b_j(y_2) \sum_{i=1}^k a_{i,j} \cdot \alpha_i(1) \end{split}$$

    $\alpha_j(T)=b_j(y_T) \sum_{i=1}^k a_{i,j} \cdot \alpha_i(T-1)$

3. 已知隐含状态链、可见状态链，求转换概率。

    知道骰子有几种（隐含状态数量），不知道每种骰子是什么（转换概率），观测到很多次掷骰子的结果（可见状态链），我想反推出每种骰子是什么（转换概率）。

    [Baum–Welch_Algorithm](https://en.wikipedia.org/wiki/Baum–Welch_algorithm):
    该算法的本质就是EM算法，因为它解决的问题就是：有了观测值X，而观测值有个隐变量Z时，求在HMM参数 λ下的联合概率P(X, Z | λ) 。

参考：

* [从贝叶斯方法谈到贝叶斯网络](http://blog.csdn.net/zdy0_2004/article/details/41096141)
* [隐马尔可夫(HMM)、前/后向算法、Viterbi算法 再次总结](http://blog.csdn.net/xueyingxue001/article/details/52396494)
* [HMM的应用与Forward算法、Viterbi算法](http://blog.csdn.net/baimafujinji/article/details/51322186)
* [如何用简单易懂的例子解释隐马尔可夫模型？ - Yang Eninala的回答 - 知乎](https://www.zhihu.com/question/20962240/answer/33438846)