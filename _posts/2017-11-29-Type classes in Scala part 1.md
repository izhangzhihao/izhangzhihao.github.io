---
layout: post
title: Type classes in Scala part 1
categories: scala
description: Type classes in Scala(转载自https://blog.scalac.io/2017/04/19/typeclasses-in-scala.html)
keywords: Type classes, Scala
---

# Type classes in Scala part 1

Type classes are a powerful and flexible concept that adds ad-hoc polymorphism to Scala. They are not a first-class citizen in the language, but other built-in mechanisms allow writing them in Scala. This is the reason why they are not so obvious to spot in code and one can have some confusion over what the ‘correct’ way of writing them is.

This blog post summarizes the idea behind type classes, how they work and the way of coding them in Scala.

## Idea

Type classes were first introduced in Haskell as a new approach to ad-hoc polymorphism. Philip Wadler and Stephen Blott describe it in [How to make ad-hoc polymorphism less ad hoc](http://homepages.inf.ed.ac.uk/wadler/papers/class/class.ps).Type classes in Haskell are an extension to the Hindley–Milner type system, implemented by that language.

Type class is a class (group) of types, **which satisfy some contract defined in a trait with addition that such functionality (trait and implementation) can be added without any changes to the original code.** One could say that the same could be achieved by extending a simple trait, but with type classes it is not necessary to predict such a need beforehand.

There is no special syntax in Scala to express a type class, but the same functionality can be achieved using constructs that already exist in the language. That’s what makes it a little difficult for newcomers to spot a type class in code. A typical implementation of a type class uses some syntactic sugar as well, which also doesn’t make it clear right away what we are dealing with.

So let’s start with baby steps to implement a type class and understand it.

## Implementation

Let’s write a type class that adds a function for getting the string representation of a given type. We add the ability for a given value to show itself. This is a `.toString` equivalent. We can start by defining a trait:

```scala
trait Show[A] {
  def show(a: A): String
}
```

We want to have `show` functionality, but defined outside of each specific type definition. Let’s start by implementing `show` for an Int.

```scala
object Show {
  val intCanShow: Show[Int] =
    new Show[Int] {
      def show(int: Int): String = s"int $int"
    }
}
```

We have defined a companion object for `Show` to add functionality there. `intCanShow` holds an implementation of `Show` trait for `Int`. This is a just the first step. Of course, usage is still very cumbersome, to use this function we have to:

```scala
println(intCanShow.show(20))
```

The full implementation containing all needed imports can be found in the [repo](https://github.com/lukeindykiewicz/typeclasses).

The next step is to write the `show` function, in `Show`’s companion object, to avoid calling `intCanShow` explicitly.

```scala
object Show {

  def show[A](a: A)(implicit sh: Show[A]) = sh.show(a)

  implicit val intCanShow: Show[Int] =
    new Show[Int] {
      def show(int: Int): String = s"int $int"
    }

}
```

The show function takes some parameter of type `A` and an implementation of the `Show` trait for that type `A`. Marking the `intCanShow` value as implicit makes the compiler able to find this implementation of `Show[A]` when there is a call to:

```scala
println(show(20))
```

That is basically a `type class`. We’re going to massage it a little bit to make it look more like real code, but all the required parts are there.**We have a trait that describes the functionality and implementations for each type we care about. There is also a function which applies an implicit instance’s function to the given parameter.**

There is a more common way of writing the `show` function having an implicit parameter. Instead of writing:

```scala
def show[A](a: A)(implicit sh: Show[A]) = sh.show(a)
```

we can use `implicitly` and rewrite it to:

```scala
def show[A: Show](a: A) = implicitly[Show[A]].show(a)
```

We also used the context bound syntax: `A: Show`, which is a syntactic sugar in Scala, mainly introduced to support type classes, it basically does the rewrite we have done above (without the use of `implicitly`), more information can be found [here](https://docs.scala-lang.org/tutorials/FAQ/context-bounds.html).

There is one more trick (convention) often used in type classes. Instead of using `implicitly` we can add an `apply` function (to the `Show` companion object) with only an implicit parameter list:

```scala
def apply[A](implicit sh: Show[A]): Show[A] = sh
```

and use it in `show` function:

```scala
def show[A: Show](a: A) = Show.apply[A].show(a)
```

This, of course, can be shortened even more:

```scala
def show[A: Show](a: A) = Show[A].show(a)
```

We can improve our type class with the possibility of calling the `show` function as if it were a method on the given object - with a simple `.show` notation. By convention it is very often called a `<TypeclassName>Ops` class.

```scala
implicit class ShowOps[A: Show](a: A) {
  def show = Show[A].show(a)
}
```

The `Ops` class allow us to write our client code like this:

```scala
println(30.show)
```

To avoid a runtime overhead it is possible to make the `ShowOps` a value class and move the type class constraint to the show function, like this:

```scala
implicit class ShowOps[A](val a: A) extends AnyVal {
  def show(implicit sh: Show[A]) = sh.show(a)
}
```

After some of the above rewrites, the companion object of `Show` looks like this:

```scala
object Show {
  def apply[A](implicit sh: Show[A]): Show[A] = sh

  def show[A: Show](a: A) = Show[A].show(a)

  implicit class ShowOps[A: Show](a: A) {
    def show = Show[A].show(a)
  }

  implicit val intCanShow: Show[Int] =
    new Show[Int] {
      def show(int: Int): String = s"int $int"
    }
}
```

Now we can add one more instance of our type class - for showing strings. It’s similar to the one showing ints of course.

```scala
implicit val stringCanShow: Show[String] =
  new Show[String]{
    def show(str: String): String = s"string $str"
  }
```

In fact, this is so similar that we want to abstract it out - what could be done with a function to create instances for different types. We can rephrase it as a “constructor” for type class instances.

```scala
def instance[A](func: A => String): Show[A] =
    new Show[A] {
      def show(a: A): String = func(a)
    }

implicit val intCanShow: Show[Int] =
    instance(int => s"int $int")

implicit val stringCanShow: Show[String] =
    instance(str => s"string $str")
```

The above snippet presents a helper function `instance` that abstracts the common code and its usage for `Int` and `String` instances. With Scala 2.12 we can use Single Abstract Methods for this purpose instead, so the code is even more concise.

```scala
implicit val intCanShow: Show[Int] =
  int => s"int $int"

implicit val stringCanShow: Show[String] =
  str => s"string $str"
```

This is the simple type class that defines two ways of calling the `show` function (`show()` and `.show`). It also defines instances for two types: `Int` and `String`.

```scala
trait Show[A] {
  def show(a: A): String
}

object Show {
  def apply[A](implicit sh: Show[A]): Show[A] = sh

  //needed only if we want to support notation: show(...)
  def show[A: Show](a: A) = Show[A].show(a)

  implicit class ShowOps[A: Show](a: A) {
    def show = Show[A].show(a)
  }

  //type class instances
  implicit val intCanShow: Show[Int] =
    int => s"int $int"

  implicit val stringCanShow: Show[String] =
    str => s"string $str"
}
```

We may encounter a need to redefine some default type class instances. With the above implementation, when all default instances were imported into scope, we can not achieve that. The compiler will have ambiguous implicits in scope and will report an error.

We may decide to move the `show` function and the `ShowOps` implicit class to another object (let say `ops`) to allow users of this type class to redefine the default instance behavior (with Category 1 implicits, more on categories of `implicits`). After such a modification, the `Show` object looks like this:

```scala
object Show {

  def apply[A](implicit sh: Show[A]): Show[A] = sh

  object ops {
    def show[A: Show](a: A) = Show[A].show(a)

    implicit class ShowOps[A: Show](a: A) {
      def show = Show[A].show(a)
    }
  }

  implicit val intCanShow: Show[Int] =
    int => s"int $int"

  implicit val stringCanShow: Show[String] =
    str => s"string $str"

}
```

Usage does not change, but now the user of this type class may import only:

```scala
import show.Show
import show.Show.ops._
```

Default implicit instances are not brought as Category 1 implicits (although they are available as Category 2 implicits), so it’s possible to define our own implicit instance where we use such a type class.

This is a basic type class that we have been coding from the very beginning.