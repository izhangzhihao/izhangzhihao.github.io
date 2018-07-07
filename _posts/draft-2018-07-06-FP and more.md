---
layout: post
title: FP and more
categories: FP Scala DeepLearning
description: 对于 Functional Programming 的一些思考
keywords: Functional Programming, FP, OOP, Scala, DeepLearning
---

## Functional Programming 印象

提到 FP 你可能会想到：immutable、pattern matching、local type inferce、lazy val、call by name等等，可能还有 Category
 Theory、Scala、Haskell、ScalaZ、Cats 等等。

## FP 与 OOP

FP 和 OOP 最根本的区别是什么呢？我认为是思维方式的不同：

OOP 是对现实世界的抽象，是归纳法。这个就不展开了...

而 FP 的核心应该是 Composability，是演绎法。像Minecraft、乐高这样的游戏、玩具，首先有一些基本的、可组合的模块，玩家可以自由组合，从而拥有无限可能性。函数式编程中的 Composability 更多的是抽象出的类型之间的转化：

![cats image](http://plastic-idolatry.com/erik/cats2.png)

而为了保证自由组合的过程中不出现期望之外的行为，Composability 会要求我们的 function 是 pure function，无副作用的 functions 组合起来就不会发生期望之外的行为。然后我们就需要满足无副作用的各种要求。

### Scalaz 和 cats

范畴论中的概念也都是可组合的，通过组合基本抽象（比如Monad、Functor）构造更高级别的抽象。

## Functional Programming and DeepLearning

这里有一篇博客[Neural Networks, Types, and Functional Programming](http://colah.github.io/posts/2015-09-NN-Types-FP/)讲神经网络其实和 FP 中的一些概念其实是类似的。这篇博客也影响了[DeepLearning.scala](https://github.com/ThoughtWorksInc/DeepLearning.scala)，DeepLearning.scala中的所有操作同时具有可微分和可组合性，这样用户就可以自由组合构建自己的神经网络了。