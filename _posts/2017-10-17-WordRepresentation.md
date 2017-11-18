---
layout: post
title: Word representation
categories: 机器学习
description: Word representation
---

# Word representation

## [One-hot representation](https://en.wikipedia.org/wiki/One-hot)

### One-hot representation 的缺点：

```
1. 纬度高(计算量大，学习速率极慢)
2. 数据稀疏(绝大部分向量的值为零,词汇鸿沟)
3. 所有的向量都正交(难以表示两个词的相似性)
```

## Distributed Representation

### Distributional Hypothesis(分布式假设)

```
linguistic items with similar distributions have similar meanings.
```

```
Distributional Representation指的是一类获取文本表示的方法，而Distributed Representation指的是文本表示的形式，就是低维、稠密的连续向量。
```

### Distributed Representation 的优点：

```
1. 纬度低
2. 连续向量，数据稠密
3. 可以表示语义空间的线性关系
```

### Embedding

Embedding在数学上表示一个maping, f: X -> Y， 也就是一个function，其中该函数是injective（就是我们所说的单射函数，每个Y只有唯一的X对应，反之亦然）和structure-preserving (结构保存，比如在X所属的空间上X1 < X2,那么映射后在Y所属空间上同理 Y1 < Y2)。那么对于word embedding，就是将单词word映射到另外一个空间，其中这个映射具有injective和structure-preserving的特点。通俗的翻译可以认为是单词嵌入，就是把X所属空间的单词映射为到Y空间的多维向量，那么该多维向量相当于嵌入到Y所属空间中，一个萝卜一个坑。word embedding，就是找到一个映射或者函数，生成在一个新的空间上的表达，该表达就是word representation。推广开来，还有image embedding, video embedding, 都是一种将源数据映射到另外一个空间

### Word Embedding

Word embedding就是在用数学的方法建模词和context之间的关系。

#### Word2Vec

用输入单词作为中心单词去预测周边单词的方式叫做：[Skip-Gram Model](http://mccormickml.com/2016/04/19/word2vec-tutorial-the-skip-gram-model/)

用输入单词作为周边单词去预测中心单词的方式叫做：[Continuous Bag of Words (CBOW)](https://iksinc.wordpress.com/tag/continuous-bag-of-words-cbow/)