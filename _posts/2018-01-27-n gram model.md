---
layout: post
title: n gram model
categories: n-gram 机器学习
description: n元语法
keywords: n-gram
math: true
---

# n元语法(n gram model)

## 句子出现的概率

n gram简单来说是用来统计一句话在整个语言中出现的概率：假如我们有一句话“我想吃苹果”，那么先分词：“我/想/吃/苹果”，那么

$$\begin{split}
P(我想吃苹果) = P(我,想,吃,苹果) = P(我)P(想|我)P(吃|我,想)P(苹果|我,想,吃)
\end{split}$$

推而广之，对于句子 $s=w_1,w_2,...,w_t$

$$\begin{split}
P(s)=P(w_1,w_2,...,w_t)=P(w_1)P(w_2|w_1)P(w_3|w_1,w_2)...P(w_t|w_1,w_2,...,w_{t−1})
\end{split}$$

## n gram model

假如严格按照上述公式计算句子出现的概率，假设总词汇大小为 N，那么第 t 个词的概率 要考虑 $$ N_{t−1} $$种情况，当 N 较大时，句子长度也较长时，计算量将是一个天文数字。我们需要找到一种办法降低计算量。
而且上述模型参数空间过大，而且数据非常稀疏。

根据**马尔科夫假设**：***当前隐含状态只取决于前一个隐含状态***

* 当 n=1 时，称为一元语法，被记为unigram，此时第 i 个词出现的概率与之前的情况完全独立。
* 当 n=2 时，称为二元语法，被称为一阶马尔科夫链，记为bigram，此时第 i 个词出现的概率与它的**前一个词有关**。
* 当 n=3 时，称为三元语法，被称为二阶马尔科夫链，记作trigram，此时第 i 个词出现的概率与它的**前两个词有关**。

对于二元语法：

$$\begin{split}
P(s)=P(w_1,w_2,...,w_t)≈P(w_1)P(w_2|w_1)P(w3|w_2)...P(w_t|w_{t−1})
\end{split}$$

另外：为了使当 t=1 时上述公式仍然有意义，可以在句子面前加上一个句子起始标记`<BOS>`，而结尾也可以添加句子结束标记`<EOS>`。

## 条件概率的计算

求
$$\begin{split}
P(w_t|w_{t−1})
\end{split}$$ 时，可直接计算 $$ w_{t−1}$$, $$ w_t $$ 在语料出现的频率并归一化。

$$ P(w_t|w_{t-1}) = \frac{count(w_{t-1}w_t)}{\sum_{w_t}count(w_{t-1}w_t)} $$

对于语料 S：{
    “我想吃苹果”,
    "他想吃橘子",
    "我想吃蓝莓"
}

对于句子“他想吃苹果”在语料 S 中出现的概率为：

$$\begin{split}
P(他想吃苹果) = P(他,想,吃,苹果) ≈ P(他|<BOS>)P(想|他)P(吃|想)P(苹果|吃)P(<EOS>|苹果)
\end{split}$$

由语料可知：

$$\begin{multline}
P(他|<BOS>) = \frac{1}{3}
\end{multline}$$

$$\begin{multline}
P(想|他) = \frac{1}{1}
\end{multline}$$

$$\begin{multline}
P(吃|想) = \frac{1}{1}
\end{multline}$$

$$\begin{multline}
P(苹果|吃) = \frac{1}{3}
\end{multline}$$

$$\begin{multline}
P(<EOS>|苹果) = \frac{1}{3}
\end{multline}$$

所以：

$$\begin{matrix}
P(他|<BOS>)P(想|他)P(吃|想)P(苹果|吃)P(<EOS>|苹果) = \frac{1}{3} \times \frac{1}{1} \times \frac{1}{1} \times \frac{1}{3} \times \frac{1}{3} = \frac{1}{27}
\end{matrix}$$

## 平滑处理

### 零概率问题

假如有词 A 和 B，而在语料中词 A B 从未连续存在过，那么
$$\begin{matrix}
P(A|B) = 0
\end{matrix}$$
，而如果 A 和 B 确实有同时出现的情况，那么P(A|B)概率为0显然不够准确。

### 平滑(smoothing)技术

最简单的办法是引入先验概率（加法平滑算法），即假设每种组合至少存在一次：

$$ P(w_t|w_{t-1}) = \frac{1 + count(w_{t-1}w_t)}{|V| + \sum_{w_t}count(w_{t-1}w_t)} $$

这里用
$$\begin{matrix}
|V|
\end{matrix}$$
来表示词汇表中单词的个数。

### 其它平滑算法

* Good-Turing估计法
* Katz平滑法
* Jelinek-Mercer平滑法
* 绝对值减法
* Kneser-Ney平滑法
* 贝叶斯平滑法