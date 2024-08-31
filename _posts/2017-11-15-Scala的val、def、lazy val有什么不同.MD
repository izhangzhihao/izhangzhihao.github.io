---
layout: post
title: Scala的val、def、lazy val有什么不同
categories: Scala
description: Scala的val、def、lazy val有什么不同，怎么选择最好的方式来定义methods/functions?
keywords: scala, val, def, var, lazy val, method, function
---

# Scala的val、def、lazy val有什么不同

``` bash
> scala
Welcome to Scala 2.12.4 (Java HotSpot(TM) 64-Bit Server VM, Java 1.8.0_144).
Type in expressions for evaluation. Or try :help.
```

``` bash
> scala@2.11
Welcome to Scala 2.11.11 (Java HotSpot(TM) 64-Bit Server VM, Java 1.8.0_144).
Type in expressions for evaluation. Or try :help.
```

## def method

* In scala 2.11.11

```scala
scala> :paste
// Entering paste mode (ctrl-D to finish)

def even: Int => Boolean = _ % 2 == 0
even eq even

// Exiting paste mode, now interpreting.

even: Int => Boolean
res0: Boolean = false

scala> System.identityHashCode(even)
res17: Int = 969115094

scala> System.identityHashCode(even)
res18: Int = 2089636626
```

Method def even evaluates on call and creates new function every time (new instance of Function1) in scala 2.11.11

* In scala 2.12.4

```scala
scala> :paste
// Entering paste mode (ctrl-D to finish)

def even: Int => Boolean = _ % 2 == 0
even eq even

// Exiting paste mode, now interpreting.

even: Int => Boolean
res0: Boolean = true

scala> System.identityHashCode(even)
res19: Int = 1803942588

scala> System.identityHashCode(even)
res20: Int = 1803942588
```

## val method

* scala 2.11.11

```scala
scala> :paste
// Entering paste mode (ctrl-D to finish)

val even: Int => Boolean = _ % 2 == 0
even eq even

// Exiting paste mode, now interpreting.

even: Int => Boolean = <function1>
res1: Boolean = true
```

* scala 2.12.4 same

## Return the same value when using val in both 2.11/2.12

* scala 2.11.11

```scala
scala> :paste
// Entering paste mode (ctrl-D to finish)

val test: () => Int = {
  val r = util.Random.nextInt
  () => r
}

// Exiting paste mode, now interpreting.

test: () => Int = <function0>

scala> test()
res2: Int = -1716734099

scala> test()
res3: Int = -1716734099
```

* 2.12.4 same

## Return new function when using def in both 2.11/2.12

* scala 2.11.11

```scala
scala> :paste
// Entering paste mode (ctrl-D to finish)

def test: () => Int = {
  val r = util.Random.nextInt
  () => r
}

// Exiting paste mode, now interpreting.

test: () => Int

scala> test()
res4: Int = -2141044486

scala> test()
res5: Int = 793997349
```

* 2.12.4 same

## val evaluates when defined

* scala 2.11.11

```scala
scala> val even: Int => Boolean = ???
scala.NotImplementedError: an implementation is missing
  at scala.Predef$.$qmark$qmark$qmark(Predef.scala:230)
  ... 32 elided
```

* 2.12.4 same

## def evaluates when called

* scala 2.11.11

```scala
scala> def even: Int => Boolean = ???
even: Int => Boolean

scala> even
scala.NotImplementedError: an implementation is missing
  at scala.Predef$.$qmark$qmark$qmark(Predef.scala:230)
  at .even(<console>:11)
  ... 32 elided

scala>
```

* 2.12.4 same

## `lazy val` evaluates only when first time been called, but return the result initialized in the first time:

* scala 2.11.11

```scala
scala> lazy val even: Int => Boolean = ???
even: Int => Boolean = <lazy>
```

```scala
scala> :paste
// Entering paste mode (ctrl-D to finish)

lazy val test: () => Int = {
  val r = util.Random.nextInt
  () => r
}

// Exiting paste mode, now interpreting.

test: () => Int = <lazy>

scala> test
res8: () => Int = <function0>

scala> test()
res9: Int = 822922251

scala> test()
res10: Int = 822922251
```

* 2.12.4 same

## 结论：

* `val`定义完后就执行。

* `def`调用时才执行，scala2.12为def做了一些优化。如果从来没调用过def定义的方法里面的代码不会执行。

* `lazy val`第一次调用时执行，以后调用就返回第一次调用初始化后的值。如果从来没调用过就永远也不会执行初始化块。但是每次调用`lazy val`定义的function时都会检查这个属性是不是已经被初始化过了...

* 如果需要定义一个**method**：

```scala
def even(i: Int): Boolean = i % 2 == 0
```

* 如果需要定义一个**function**，为了避免每次传递这个function都会创建一个新的对象(scala 2.11):

```scala
val even: Int => Boolean = _ % 2 == 0
```

* 而对于scala2.12 不需要特别考虑上面两个规则，因为scala2.12不会为def定义的方法每次调用时都生成新的function，但是为了保持语义上的同一建议还是参考上面两条建议。