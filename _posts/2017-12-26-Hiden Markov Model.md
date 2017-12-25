---
layout: post
title: 隐马尔可夫模型
categories: 机器学习
description: 隐马尔可夫模型
keywords: HMM, Hiden Markov Model
mathjax: true
---

## 信念网络（BN：Belief Network

信念网络也叫做贝叶斯网络或贝叶斯信念网络。

p(x_1, ..., x_D) = \prod_{i=1}^D p(x_i|pa(x_i)) \\
pa(x) = x的父母(parents)

![](/assets/images/BN.png)

信念网络可表示成一个有向无环图（DAG）。



隐马尔科夫模型（Hidden Markov Model）经常被用在时间序列（例如一段时间内的声音信号，运动物体的位置等你所感兴趣的物理量）的建模与分析。

它有三个要素:

1. 可见随机变量：用来描述你所感兴趣的物理量，随时间变化
2. 隐含的状态变量：每个时间点的物理量背后都对应的不可见的状态量
3. 变量间的关系：用概率的方法（通常是概率密度函数）描述以下三个关系或变量：
    * 初始状态量
    * 当前的隐含状态量与下一个隐含状态量间关系（此处还用到马尔科夫假设：***当前隐含状态只取决于前一个隐含状态***）
    * 当前的隐含状态量与可见随机量间关系




参考：

* [如何用简单易懂的例子解释隐马尔可夫模型？ - Yang Eninala的回答 - 知乎](https://www.zhihu.com/question/20962240/answer/33438846)