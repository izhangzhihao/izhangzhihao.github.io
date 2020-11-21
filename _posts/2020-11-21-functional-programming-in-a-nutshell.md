---
layout: slide
title: Functional Programming In A Nutshell
theme: night
# beige black blood league moon night serif simple sky solarized white
# taken from https://github.com/scateu/scateu.github.io/blob/master/_posts/2016-12-30-reveal-demo.md
transition: slide 
# none/fade/slide/convex/concave/zoom  #concave可能会使iOS的Safari崩溃
ruby_notation: true
diagram: true
mathjax: true
multiplex:
  id: 99702b17e6a745f9 #Secret: 14830192734523284320
  url: https://multiplex.scateu.me
  #url: https://reveal-js-multiplex-ccjbegmaii.now.sh
# DONE: 把rogue着色器在这里关掉，或者在全局全部用highlight.js来着色
# TODO: https://github.com/webpro/reveal-md
# DONE: 想一个更优雅的办法来处理multiplex
# HELP: <section markdown="1"> </section>为分隔
# HELP: <aside class="notes"> </aside> 为注记
---

<section markdown="1">

# Functional Programming In A Nutshell

</section>

<section markdown="1">

## Complexity Is your Enemy

> Functional programming is a programming paradigm — a style of building the structure and elements of computer programs — that treats computation as the evaluation of mathematical functions and avoids changing-state and mutable data — Wikipedia

</section>

<section markdown="1">

### 什么是函数式编程

- {:.fragment} “函数式”是什么？ 什么是“纯函数”？
- {:.fragment} 什么不函数式？side effect是什么？
- {:.fragment} 函数式的衍生特性
- {:.fragment} 函数作为一等公民
- {:.fragment} Currying, Uncurrying and Compose
- {:.fragment} 可变=可怕😨
- {:.fragment} 函数式建模
- {:.fragment} 错误的表达方式
- {:.fragment} 函数式思维模式：模式匹配
- {:.fragment} 函数式思维模式：使用递归而不是循环
- {:.fragment} 函数式思维模式：严格求值和惰性求值
- {:.fragment} 这些关键词你应该忘掉
- {:.fragment} FP & OOP
- {:.fragment} 从“表达式问题”说起，什么是typeclass？
- {:.fragment} 函数式编程与分布式（CRDTs）
- {:.fragment} 范畴论
</section>

<section markdown="1">

## “函数”是什么？

### 数学上的函数: 

`y=f(x)`

### 数学函数的特征：

* 作用域
* 值域
* total
* deterministic
* 无副作用
* pure

</section>


<section markdown="1">

## 副作用是什么？

* 修改变量
* in-place修改一个对象的值
* 抛出异常
* 打印日志/照片
* 读写文件/数据库
* GUI应用
* ...

</section>


<section markdown="1">

## 函数式的衍生特性

* 引用透明
  * 代替
    * 可缓存
* 可组合性
* 可推断(reasonable)
* 容易并行

</section>

<section markdown="1">

## 函数作为一等公民

* 函数作为值
* 函数不必依赖于其他的存在（class/record...）
* 函数可以作为参数

</section>

<section markdown="1">

## Currying, Uncurrying and Compose

```
def partial[A,B,C](a: A, f: (A, B) => C): B => C =
    (b: B) => f(a, b)

def currying[A,B,C](f: (A, B) => C): A => B => C =
    a => b => f(a, b)

def uncurrying[A,B,C](f: A => B => C): (A, B) => C =
    (a, b) => f(a)(b)

def compose[A,B,C](f: B => C, g: A => B): A => C =
    a => f(g(a))
```

</section>


<section markdown="1">

## (im)mutable reference and (im)mutable object 


</section>


<section markdown="1">

## 函数式建模 --类型驱动的思维模式

```
sealed trait List[+A]
case object Nil extends List[Nothing]
case class Cons[+A](head: A, tail: List[A]) extends List[A]
```

//（List，模式匹配，Either，Optional，Error）


</section>


<section markdown="1">

## 错误的表达方式 -- Either，Option

```
sealed trait Option[+A]
case class Some[+A](get: A) extends Option[A]
case object None extends Option[Nothing]
```


```
sealed trait Either[+E,+A]
case class Left[+E](get: E) extends Either[E,Nothing]
case class Right[+A](get: A) extends Either[Nothing,A]
```


</section>


<section markdown="1">

## 模式匹配

```
  def sum(ints: List[Int]): Int = ints match {
    case Nil => 0
    case Cons(x,xs) => x + sum(xs)
  }
```

</section>

<section markdown="1">

## 使用递归而不是循环

```
def factorial2(n: Int): Int = {
    var acc = 1
    var i = n
    while (i > 0) { acc *= i; i -= 1 }
    acc
}

def factorial(n: Int): Int = {
    @annotation.tailrec
    def go(n: Int, acc: Int): Int =
      if (n <= 0) acc
      else go(n-1, n*acc)

    go(n, 1)
}
```

</section>

<section markdown="1">

## 严格求值和惰性求值

```

```

</section>



<section markdown="1">

## FP 与 OOP

FP 和 OOP 最根本的区别是什么呢？我认为是思维方式的不同：

OOP 是对现实世界的抽象，是归纳法。这个就不展开了...

而 FP 的核心应该是 Composability，是演绎法。像Minecraft、乐高这样的游戏、玩具，首先有一些基本的、可组合的模块，玩家可以自由组合，从而拥有无限可能性。函数式编程中的 Composability 更多的是抽象出的类型之间的转化：

![cats image](http://plastic-idolatry.com/erik/cats2.png)

</section>

<section markdown="1">

函数式编程对内部的“pure”和对外部的“side effect”与一句话有异曲同工之妙 --

> 在组织内部不会有成果出现，一切成果都是发生在组织外部。

</section>


<section markdown="1">

## Thanks

</section>
