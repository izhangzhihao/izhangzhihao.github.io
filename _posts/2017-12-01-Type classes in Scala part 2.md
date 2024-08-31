---
layout: post
title: Type classes in Scala part 2
categories: Scala
description: Type classes in Scala(转载自https://blog.scalac.io/2017/04/19/typeclasses-in-scala.html)
keywords: Type classes, Scala
reproduced: https://blog.scalac.io/2017/04/19/typeclasses-in-scala.html
---

# Type classes in Scala part 2

## Own types

The creator of a type class very often provides its instances for popular types, but our own types are not always supported out of the box (it depends on the library provider, whether some kind of products/coproducts derivation is implemented). Nothing stops us from writing our implementation for the type class. That, of course, looks exactly the same as if we would like to redefine, for some reason, the default instance that was provided by the implementer of the type class. The code implementing our own instance follows the same pattern, but could be implemented in a different location than the trait and the ops classes of the type class. On the other hand, if the type class is in our code base we may add this instance next to default instances defined in type class trait’s **companion object**. As an example, let’s define a way to show a `Foo` case class and its instance outside of the type class companion object:

```scala
package mainshow

import show.Show
import show.Show.ops._

object MainShow extends App {

  case class Foo(foo: Int)

  implicit val fooShow: Show[Foo] =
    foo => s"case class Foo(foo: ${foo.foo})"

  println(30.show)
  println(Foo(42).show)

}
```

## Shapeless

This paragraph is a little off topic, but I think it’s worth mentioning.

The way type classes are implemented in Scala (with implicits) makes it possible to automatically derive type class instances for our own created types using [Shapeless](https://github.com/milessabin/shapeless). For example, we could derive the `show` function (from previous paragraphs) for every case class (actually for every product type) defined in our code. We would need to define instances for basic types and how to define `show` for product types, but it will reduce so much boilerplate in our code!

Just for completeness of the information, similar derivation can be achieved with **runtime reflection or compile-time macros**.

## Simulacrum

[Simulacrum](https://github.com/mpilquist/simulacrum) is a project that adds syntax for type classes using macros. Whether to use it or not depends on your personal judgement. If it is used, it’s trivial to find all type classes in our code and it reduces some boilerplate. On the other hand, a project that uses `@typeclass` has to depend on the macro paradise compiler plugin.

The equivalent of our `Show` example with an instance only for `String` would look like this:

```scala
import simulacrum._

@typeclass trait ShowSim[A] {
  def showSim(a: A): String
}

object ShowSim {
  implicit val stringCanShow: ShowSim[String] =
    str => s"simulacrum string $str"
}
```

As you can see, the definition of a type class is very concise. On the usage side nothing changes - we would use it like this:

```scala
println("bar".showSim)
```

There is an additional annotation `@op` that may change the name of generated function and/or add some alias to generated method (i.e. `|+|` notation for summing).

Proper imports can be found in [repo](https://github.com/lukeindykiewicz/typeclasses).

## Implicits

Type classes use implicits as a mechanism for matching instances with code that uses them. Type classes come with all the benefits and costs related to implicits.

It is possible to define multiple instances of a type class for the same type. The compiler uses implicit resolution to find an instance that is the closest in the scope. As a comparison, a type class in Haskell can only have one instance. In Scala, we can define an instance and pass it as a parameter explicitly (not relying on implicit resolution), which makes the usage less convenient, but sometimes may be useful.

Our `Show` example needs a little modification to allow usage in a scenario, where we would like to pass instances explicitly. Let’s add a `showExp` function to the `ShowOps` class:

```scala
def showExp(implicit sh: Show[A]) = sh.show(a)
```

Now, it’s possible to just run the `.showExp` function or define and provide an instance of `Show` to `showExp` explicitly:

```scala
val hipsterString: Show[String] =
  str => s"hipster string $str."

println("baz".showExp)  // prints: string baz
println("baz".showExp(hipsterString)) // prints: hipster string baz
```

The first invocation uses the implicit found in scope, to the second invocation we pass the `hipsterString` instance of `Show`.

The other way (more common) to achieve the same result - without adding an extra function, but fully relying on implicits - **is to create a Category 1 implicit that would take precedence over the default instance (a Category 2 implicit)**. This would look like this:

```scala
println("baz".show)

{
  implicit val hipsterString: Show[String] =
    str => s"hipster string $str."

  println("bazbaz".show)
}
```

`"baz"` would use the default instance defined in `Show`, but `"bazbaz"` would use `hipsterString` instance.

The Scala way of implementing a type class (using implicits) could also cause some problems, which are described in the next paragraph.

## Problems

With the power of implicits there comes a cost.

We can’t have two type class instances for some type T with the same precedence. This doesn’t sound like a terrible problem, but in practice it causes some real issues. It’s quite easy to get a compiler error (about ambiguous implicits) when using libraries like `Cats` or `Scalaz`, which heavily rely on type classes and build their types as a hierarchy (by subtyping). That is in detail described [here](http://typelevel.org/blog/2016/09/30/subtype-typeclasses.html). The problem is mainly related to the way type classes are implemented. Very often both ambiguous implicits implement exactly the same behavior, but the compiler can’t know about it. There are ongoing [discussions](https://github.com/lampepfl/dotty/issues/2047) on how to fix this.

Errors may also be misleading, because the compiler doesn’t know what a type class is, e.g. for our `Show` type class used in such a way:

```scala
true.show
```

compiler can only say that `value show is not a member of Boolean`.

A similar error message is even reported when ambiguous implicit definitions are found, but the `.show` notation was used.

## Open source examples

Open source is a perfect place to look for examples of type classes. I would like to name two projects:

* Cats

[Cats](https://github.com/typelevel/cats) uses type classes as a primary way to model things. This lib uses also simulacrum. Instances are implemented in separate traits, also Ops are grouped in syntax traits.

* Shapeless

[Shapeless](https://github.com/milessabin/shapeless) heavily relies on type classes. The power of shapeless is the ability to work on `HLists` and derive type classes to add new functionality.

## Future of type classes

There are different attempts and discussions on how to add syntax for type classes:

* [Think about type class/implicits encoding](https://github.com/lampepfl/dotty/issues/2029)
* [Simulacrum](https://github.com/mpilquist/simulacrum)
* [Dandy](https://github.com/maxaf/dandy)

There are also some ongoing discussion on coherence of type classes:

* [Allow Typeclasses to Declare Themselves Coherent](https://github.com/lampepfl/dotty/issues/2047)

## Summary

Type classes as a concept are quite easy, but there are various corner cases when it comes to its implementation in Scala. The concept is rather used in libraries than in business applications, but it’s good to know type classes and potential risks of using them.