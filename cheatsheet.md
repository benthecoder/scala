# cheatsheet

This cheat sheet originated from the forum, credits to Laurent Poulain. [Source](https://github.com/lampepfl/progfun-wiki/blob/gh-pages/CheatSheet.md)

- [cheatsheet](#cheatsheet)
  - [Evaluation Rules](#evaluation-rules)
  - [Higher order functions](#higher-order-functions)
  - [Currying](#currying)
  - [Classes](#classes)
  - [End markers](#end-markers)
  - [Operators](#operators)
  - [Class hierarchies](#class-hierarchies)
  - [Class Organization](#class-organization)
  - [Type Parameters](#type-parameters)
  - [Variance](#variance)
  - [Pattern Matching](#pattern-matching)
    - [Options](#options)
    - [Pattern Matching in Anonymous Functions](#pattern-matching-in-anonymous-functions)
  - [Collections](#collections)
    - [Base Classes](#base-classes)
    - [Immutable Collections](#immutable-collections)
    - [Mutable Collections](#mutable-collections)
    - [Examples](#examples)
  - [Pairs (similar for larger Tuples)](#pairs-similar-for-larger-tuples)
  - [Ordering](#ordering)
  - [For-Comprehensions](#for-comprehensions)
    - [Example 1](#example-1)
    - [Translation Rules](#translation-rules)
    - [Example 2](#example-2)
  - [Style guide (Common Issues)](#style-guide-common-issues)
    - [#1 Avoid Casts and Type Tests](#1-avoid-casts-and-type-tests)
    - [#2 Indentation](#2-indentation)
    - [#3 Line Length and Whitespace](#3-line-length-and-whitespace)
    - [#4 Use local Values to simplify complex Expressions](#4-use-local-values-to-simplify-complex-expressions)
    - [#5 Choose meaningful Names for Methods and Values](#5-choose-meaningful-names-for-methods-and-values)
    - [#6 Common Subexpressions](#6-common-subexpressions)
    - [#7 Don't Copy-Paste Code!](#7-dont-copy-paste-code)
    - [#8 Scala doesn't require Semicolons](#8-scala-doesnt-require-semicolons)
    - [#9 Don't submit Code with "print" Statements](#9-dont-submit-code-with-print-statements)
    - [#10 Avoid using Return](#10-avoid-using-return)
    - [#11 Avoid mutable local Variables](#11-avoid-mutable-local-variables)
    - [#12 Eliminate redundant "If" Expressions](#12-eliminate-redundant-if-expressions)

## Evaluation Rules

- Call by value: evaluates the function arguments before calling the function
- Call by name: evaluates the function first, and then evaluates the arguments if need be

<!-- code -->

```scala
    def example = 2      // evaluated when called
    val example = 2      // evaluated immediately
    lazy val example = 2 // evaluated once when needed

    def square(x: Double)    // call by value
    def square(x: => Double) // call by name
    def myFct(bindings: Int*) =  ...  // bindings is a sequence of int, containing a varying # of arguments
```

## Higher order functions

These are functions that take a function as a parameter or return functions.

```scala
    // sum takes a function that takes an integer and returns an integer then
    // returns a function that takes two integers and returns an integer
    def sum(f: Int => Int): (Int, Int) => Int =
      def sumf(a: Int, b: Int): Int = f(a) + f(b)
      sumf

    // same as above. Its type is (Int => Int) => (Int, Int) => Int
    def sum(f: Int => Int)(a: Int, b: Int): Int =  ...

    // Called like this
    sum((x: Int) => x * x * x)          // Anonymous function, i.e. does not have a name
    sum(x => x * x * x)                 // Same anonymous function with type inferred

    def cube(x: Int) = x * x * x
    sum(x => x * x * x)(1, 10) // sum of 1 cubed and 10 cubed
    sum(cube)(1, 10)           // same as above
```

## Currying

Converting a function with multiple arguments into a function with a
single argument that returns another function.

```scala
    def f(a: Int, b: Int): Int // uncurried version (type is (Int, Int) => Int)
    def f(a: Int)(b: Int): Int // curried version (type is Int => Int => Int)
```

To curry an existing function :

```scala
    val f2: (Int, Int) => Int = f // uncurried version (type is (Int, Int) => Int)
    val f3: Int => Int => Int = f2.curried // transform it to a curried version (type is Int => Int => Int)
    val f4: (Int, Int) => Int = Function.uncurried(f3) // go back to the uncurried version (type is (Int, Int) => Int)
```

## Classes

```scala
    class MyClass(x: Int, val y: Int,
                          var z: Int):        // Defines a new type MyClass with a constructor
                                              // x will not be available outside MyClass
                                              // val will generate a getter for y
                                              // var will generate a getter and a setter for z
      require(y > 0, "y must be positive")    // precondition, triggering an IllegalArgumentException if not met
      def this (x: Int) =  ...                // auxiliary constructor
      def nb1 = x                             // public method computed every time it is called
      private def test(a: Int): Int =  ...    // private method
      val nb3 = x + y                         // computed only once
      override def toString =                 // overridden method
          x + ", " + y
    end MyClass

    new MyClass(1, 2, 3) // creates a new object of type
```

`this` references the current object, `assert(<condition>)` issues `AssertionError` if condition
is not met. See [`scala.Predef`](https://www.scala-lang.org/api/current/scala/Predef$.html) for `require`, `assume` and `assert`.

## End markers

When the body of a class, object, trait, method or value becomes long, visually
inspecting where it ends might become challenging.
In these situations, it is possible to explicitly signal to the reader that the body
is over using the `end` keyword with the name of the definition:

```scala
    class MyClass(a: Int, b: String):
      // body
    end MyClass

    object MyObject:
      // body
    end MyObject

    trait MyTrait:
      // body
    end MyTrait

    def myMethod(name: String): Unit =
      println(s"Hello $name")
    end myMethod

    val myVal: Int =
      42
    end myVal
```

## Operators

`myObject myMethod 1` is the same as calling `myObject.myMethod(1)`

Operator (i.e. function) names can be alphanumeric, symbolic (e.g. `x1`, `*`, `+?%&`, `vector_++`, `counter_=`)

The precedence of an operator is determined by its first character, with the following increasing order of priority:

    (all letters)
    |
    ^
    &
    < >
    = !
    :
    + -
    * / %
    (all other special characters)

The associativity of an operator is determined by its last character: Right-associative if ending with `:`, Left-associative otherwise.

Note that assignment operators have lowest precedence. (Read Scala Language Specification 2.9 sections 6.12.3, 6.12.4 for more info)

## Class hierarchies

```scala
    abstract class TopLevel:     // abstract class
      def method1(x: Int): Int   // abstract method
      def method2(x: Int): Int =  ...
    end TopLevel

    class Level1 extends TopLevel:
      def method1(x: Int): Int = ...
      override def method2(x: Int): Int = ... // TopLevel's method2 needs to be explicitly overridden
    end Level1

    object MyObject extends TopLevel:
      ...  // defines a singleton object. No other instance can be created
```

To create a runnable application in Scala:

```scala
    @main def run(args: Array[String]) =
      println("Hello world")
```

or

```scala
    object Hello extends App:
      println("Hello World")
```

## Class Organization

- Classes and objects are organized in packages (`package myPackage`).

- They can be referenced through import statements (`import myPackage.MyClass`, `import myPackage.*`,
  `import myPackage.{MyClass1, MyClass2}`, `import myPackage.{MyClass1 => A}`)

- They can also be directly referenced in the code with the fully qualified name (`new myPackage.MyClass1`)

- All members of packages `scala` and `java.lang` as well as all members of the object `scala.Predef` are automatically imported.

- Traits are similar to Java interfaces, except they can have non-abstract members:

```scala
        trait Planar:
	   ...
        class Square extends Shape with Planar
```

- General object hierarchy:

  - `scala.Any` base type of all types. Has methods `hashCode` and `toString` that can be overridden
  - `scala.AnyVal` base type of all primitive types. (`scala.Double`, `scala.Float`, etc.)
  - `scala.AnyRef` base type of all reference types. (alias of `java.lang.Object`, supertype of `java.lang.String`, `scala.List`, any user-defined class)
  - `scala.Null` is a subtype of any `scala.AnyRef` (`null` is the only instance of type `Null`), and `scala.Nothing` is a subtype of any other type without any instance.

## Type Parameters

Conceptually similar to C++ templates or Java generics. These can apply to classes, traits or functions.

```scala
    class MyClass[T](arg1: T):
      ...

    MyClass[Int](1)
    MyClass(1)   // the type is being inferred, i.e. determined based on the value arguments
```

It is possible to restrict the type being used, e.g.

```scala
    def myFct[T <: TopLevel](arg: T): T = ... // T must derive from TopLevel or be TopLevel
    def myFct[T >: Level1](arg: T): T = ...   // T must be a supertype of Level1
    def myFct[T >: Level1 <: TopLevel](arg: T): T = ...
```

## Variance

Given `A <: B`

If `C[A] <: C[B]`, `C` is covariant

If `C[A] >: C[B]`, `C` is contravariant

Otherwise C is nonvariant

```scala
    class C[+A]  // C is covariant
    class C[-A]  // C is contravariant
    class C[A]   // C is nonvariant
```

For a function, if `A2 <: A1` and `B1 <: B2`, then `A1 => B1 <: A2 => B2`.

Functions must be contravariant in their argument types and covariant in their result types, e.g.

```scala
    trait Function1[-T, +U]:
      def apply(x: T): U
    // Variance check is OK because T is contravariant and U is covariant

    class Array[+T]:
      def update(x: T) // variance checks fails
```

Find out more about variance in
[lecture 4.4](https://class.coursera.org/progfun-2012-001/lecture/81)
and [lecture 4.5](https://class.coursera.org/progfun-2012-001/lecture/83)

## Pattern Matching

Pattern matching is used for decomposing data structures:

```scala
    unknownObject match
      case MyClass(n) => ...
      case MyClass2(a, b) => ...
```

Here are a few example patterns

```scala
    (someList: List[T]) match
      case Nil => ...          // empty list
      case x :: Nil => ...     // list with only one element
      case List(x) => ...      // same as above
      case x :: xs => ...      // a list with at least one element. x is bound to the head,
                               // xs to the tail. xs could be Nil or some other list.
      case 1 :: 2 :: cs => ... // lists that starts with 1 and then 2
      case (x, y) :: ps => ... // a list where the head element is a pair
      case _ => ...            // default case if none of the above matches
```

The last example shows that every pattern consists of sub-patterns: it
only matches lists with at least one element, where that element is a
pair. `x` and `y` are again patterns that could match only specific
types.

### Options

Pattern matching can also be used for `Option` values. Some
functions (like `Map.get`) return a value of type `Option[T]` which
is either a value of type `Some[T]` or the value `None`:

```scala
    val myMap = Map("a" -> 42, "b" -> 43)

    def getMapValue(s: String): String =
      myMap get s match
        case Some(nb) => "Value found: " + nb
        case None => "No value found"

    getMapValue("a")  // "Value found: 42"
    getMapValue("c")  // "No value found"
```

Most of the times when you write a pattern match on an option value,
the same expression can be written more concisely using combinator
methods of the `Option` class. For example, the function `getMapValue`
can be written as follows:

```scala
    def getMapValue(s: String): String =
      myMap.get(s).map("Value found: " + _).getOrElse("No value found")
```

### Pattern Matching in Anonymous Functions

Pattern matches are also used quite often in anonymous functions:

```scala
    val options: List[Option[Char]] = Some('a') :: None :: Some('b') :: Nil
    val chars: List[Char] = options.map(o => o match {
      case Some(ch) => ch
      case None => 'z'
    })
```

Instead of `o => o match { case ... }`, you can simply write `{case ...}`, so the above example becomes more concise:

```scala
    val chars: List[Char] = options.map {
      case Some(ch) => ch
      case None => 'z'
    }
```

## Collections

Scala defines several collection classes:

### Base Classes

- [`Iterable`](https://www.scala-lang.org/api/current/scala/collection/Iterable.html) (collections you can iterate on)
- [`Seq`](https://www.scala-lang.org/api/current/scala/collection/Seq.html) (ordered sequences)
- [`Set`](https://www.scala-lang.org/api/current/scala/collection/Set.html)
- [`Map`](https://www.scala-lang.org/api/current/scala/collection/Map.html) (lookup data structure)

### Immutable Collections

- [`List`](https://www.scala-lang.org/api/current/scala/collection/immutable/List.html) (linked list, provides fast sequential access)
- [`Stream`](https://www.scala-lang.org/api/current/scala/collection/immutable/Stream.html) (same as List, except that the tail is evaluated only on demand)
- [`Vector`](https://www.scala-lang.org/api/current/scala/collection/immutable/Vector.html) (array-like type, implemented as tree of blocks, provides fast random access)
- [`Range`](https://www.scala-lang.org/api/current/scala/collection/immutable/Range.html) (ordered sequence of integers with equal spacing)
- [`String`](http://docs.oracle.com/javase/1.5.0/docs/api/java/lang/String.html) (Java type, implicitly converted to a character sequence, so you can treat every string like a `Seq[Char]`)
- [`Map`](https://www.scala-lang.org/api/current/scala/collection/immutable/Map.html) (collection that maps keys to values)
- [`Set`](https://www.scala-lang.org/api/current/scala/collection/immutable/Set.html) (collection without duplicate elements)

### Mutable Collections

Most of the immutable collections above have a mutable counterpart, e.g.:

- [`Array`](https://www.scala-lang.org/api/current/scala/Array.html) (Scala arrays are native JVM arrays at runtime, therefore they are very performant)
- Scala also has mutable maps and sets; these should only be used if there are performance issues with immutable types

### Examples

NOTE: For the correct code convention of using postfix or not, read [this](https://docs.scala-lang.org/style/method-invocation.html)

```scala
    val fruitList = List("apples", "oranges", "pears")
    // Alternative syntax for lists
    val fruit = "apples" :: ("oranges" :: ("pears" :: Nil)) // parens optional, :: (pronounced cons) is right-associative
    fruit.head   // "apples"
    fruit.tail   // List("oranges", "pears")
    val empty = List()
    val empty = Nil

    val nums = Vector("louis", "frank", "hiromi")
    nums(1)                     // element at index 1, returns "frank", complexity O(log(n))
    nums.updated(2, "helena")   // new vector with a different string at index 2, complexity O(log(n))

    val fruitSet = Set("apple", "banana", "pear", "banana")
    fruitSet.size    // returns 3: there are no duplicates, only one banana

    val r: Range = 1 until 5 // 1, 2, 3, 4
    val s: Range = 1 to 5    // 1, 2, 3, 4, 5
    1 to 10 by 3  // 1, 4, 7, 10
    6 to 1 by -2  // 6, 4, 2

    val s = (1 to 6).toSet
    s.map(_ + 2) // adds 2 to each element of the set

    val s = "Hello World"
    s filter (c => c.isUpper) // returns "HW"; strings can be treated as Seq[Char]

    // Operations on sequences
    val xs = List(...)
    xs.length         // number of elements, complexity O(n)
    xs.last           // last element (exception if xs is empty), complexity O(n)
    xs.init           // all elements of xs but the last (exception if xs is empty), complexity O(n)
    xs take n         // first n elements of xs
    xs drop n         // the rest of the collection after taking n elements
    xs splitAt n      // same as (xs take n, xs drop n)
    xs(n)             // the nth element of xs, complexity O(n)
    xs ++ ys          // concatenation, complexity O(n)
    xs.reverse        // reverse the order, complexity O(n)
    xs updated(n, x)  // same list as xs, except at index n where it contains x, complexity O(n)
    xs indexOf x      // the index of the first element equal to x (-1 otherwise)
    xs contains x     // same as xs indexOf x >= 0
    xs filter p       // returns a list of the elements that satisfy the predicate p
    xs filterNot p    // filter with negated p
    xs partition p    // same as (xs filter p, xs filterNot p)
    xs takeWhile p    // the longest prefix consisting of elements that satisfy p
    xs dropWhile p    // the remainder of the list after any leading element satisfying p have been removed
    xs span p         // same as (xs takeWhile p, xs dropWhile p)

    List(x1, ..., xn) reduceLeft op    // (...(x1 op x2) op x3) op ...) op xn
    List(x1, ..., xn).foldLeft(z)(op)  // (...( z op x1) op x2) op ...) op xn
    List(x1, ..., xn) reduceRight op   // x1 op (... (x{n-1} op xn) ...)
    List(x1, ..., xn).foldRight(z)(op) // x1 op (... (    xn op  z) ...)

    xs exists p    // true if there is at least one element for which predicate p is true
    xs forall p    // true if p(x) is true for all elements
    xs zip ys      // returns a list of pairs which groups elements with same index together
    xs.unzip       // opposite of zip: returns a pair of two lists
    xs flatMap f   // applies the function to all elements and concatenates the result
    xs.sum         // sum of elements of the numeric collection
    xs.product     // product of elements of the numeric collection
    xs.max         // maximum of collection
    xs.min         // minimum of collection
    xs.flatten     // flattens a collection of collection into a single-level collection
    xs groupBy f   // returns a map which points to a list of elements
    xs.distinct    // sequence of distinct entries (removes duplicates)

    x +: xs  // creates a new collection with leading element x
    xs :+ x  // creates a new collection with trailing element x

    // Operations on maps
    val myMap = Map("I" -> 1, "V" -> 5, "X" -> 10)  // create a map
    myMap("I")      // => 1
    myMap("A")      // => java.util.NoSuchElementException
    myMap get "A"   // => None
    myMap get "I"   // => Some(1)
    myMap.updated("V", 15)  // returns a new map where "V" maps to 15 (entry is updated)
                            // if the key ("V" here) does not exist, a new entry is added

    // Operations on LazyLists
    val xs = LazyList(1, 2, 3)
    val xs = LazyList.cons(1, LazyList.cons(2, LazyList.cons(3, LazyList.empty))) // same as above
    (1 to 1000).to[LazyList] // => Stream(1, ?)
    x #:: xs // Same as LazyList.cons(x, xs)
             // In the LazyList's cons operator, the second parameter (the tail)
             // is defined as a "call by name" parameter.
             // Note that x::xs always produces a List
    def integers(start: Int = 0): LazyList[Int] = start #:: integers(start + 1) // infinite sequence of integers starting at "start"
    integers(0) drop 10 take 100 // New lazylist starting at 10
```

## Pairs (similar for larger Tuples)

```scala
    val pair = ("answer", 42)   // type: (String, Int)
    val (label, value) = pair   // label = "answer", value = 42
    pair(0) // "answer"
    pair(1) // 42
```

## Ordering

There is already a class in the standard library that represents orderings: `scala.math.Ordering[T]` which contains
comparison functions such as `lt()` and `gt()` for standard types. Types with a single natural ordering should inherit from
the trait `scala.math.Ordered[T]`.

```scala
    import math.Ordering

    def msort[T](xs: List[T])(using Ordering[T]) = ...
    msort(fruits)(using Ordering.String)
    msort(fruits)  // the compiler figures out the right ordering
```

## For-Comprehensions

A for-comprehension is syntactic sugar for `map`, `flatMap` and `filter` operations on collections.

The general form is `for (s) yield e`

- `s` is a sequence of generators and filters
- `p <- e` is a generator
- `if f` is a filter
- If there are several generators (equivalent of a nested loop), the last generator varies faster than the first
- You can use `{ s }` instead of `( s )` if you want to use multiple lines without requiring semicolons
- `e` is an element of the resulting collection

### Example 1

```scala
    // list all combinations of numbers x and y where x is drawn from
    // 1 to M and y is drawn from 1 to N
    for (x <- 1 to M; y <- 1 to N)
      yield (x,y)
```

is equivalent to

```scala
    (1 to M).flatMap(x => (1 to N).map(y => (x, y)))
```

### Translation Rules

A for-expression looks like a traditional for loop but works differently internally

`for (x <- e1) yield e2` is translated to `e1.map(x => e2)`

`for (x <- e1 if f; s) yield e2` is translated to `for (x <- e1.withFilter(x => f); s) yield e2`

`for (x <- e1; y <- e2; s) yield e3` is translated to `e1.flatMap(x => for (y <- e2; s) yield e3)`

Note: s is a (potentially empty) sequence of fields (generators and filters)

This means you can use a for-comprehension for your own type, as long
as you define `map`, `flatMap` and `filter`.

For more, see [lecture 6.5](https://class.coursera.org/progfun-2012-001/lecture/111).

### Example 2

```scala
    for
      i <- 1 until n
      j <- 1 until i
      if isPrime(i + j)
    yield (i, j)
```

is equivalent to

```scala
    for (i <- 1 until n; j <- 1 until i if isPrime(i + j))
        yield (i, j)
```

is equivalent to

```scala
    (1 until n).flatMap(i => (1 until i).filter(j => isPrime(i + j)).map(j => (i, j)))
```

## Style guide (Common Issues)

### #1 Avoid Casts and Type Tests

Never use `isInstanceOf` or `asInstanceOf` - there's always a better solution, both for the assignments, and also for any real-world Scala project.
If you find yourself wanting to use casts, take a step back and think about what you're trying to achieve.
Re-read the assignment instructions and have another look at the corresponding lecture videos.

### #2 Indentation

Make sure your code is properly indented, it becomes a lot more readable.

This might seem trivial and not very relevant for our exercises, but imagine yourself in the future being part of a team, working on the same files with other coders: it is very important that everybody respects the style rules to keep the code healthy.

If your editor does not do indentation the way you would like it to have, you should find out how to change its settings. In Scala, the standard is to indent using 2 spaces (no tabs).

### #3 Line Length and Whitespace

Make sure the lines are not too long, otherwise your code is very hard to read. Instead of writing very long lines, introduce some local value bindings. Using whitespace uniformly makes your code more readable.

Example (long line, missing spaces):

    if(p(this.head))this.tail.filter0(p, accu.incl(this.head))else this.tail.filter0(p, accu)

Better:

    if (p(this.head))
      this.tail.filter0(p, accu.incl(this.head))
    else
      this.tail.filter0(p, accu)

Even better (see #4 and #6 below):

    val newAccu =
      if (p(this.head)) accu.incl(this.head)
      else accu
    this.tail.filter0(p, newAccu)

### #4 Use local Values to simplify complex Expressions

When writing code in functional style, methods are often implemented as a combination of function calls.
If such a combined expression grows too big, the code might become hard to understand.

In such cases it is better to store some arguments in a local value before passing them to the function (see #3 above).
Make sure that the local value has a meaningful name (see #5 below)!

### #5 Choose meaningful Names for Methods and Values

The names of methods, fields and values should be carefully chosen so that the source code is easy to understand.
A method name should make it clear what the method does.
No, `temp` is never a good name :-)

A few improvable examples:

    val temp = sortFuntion0(list.head, tweet)   // what does sortFunction0 do?
    def temp(first: TweetSet, second : TweetSet): TweetSet = ...
    def un(th: TweetSet,acc: TweetSet): TweetSet = ...
    val c = if (p(elem)) accu.incl(elem) else accu
    def loop(accu: Trending, current: TweetSet): Trending = ...
    def help(ch: Char, L2: List[Char], compteur: Int): (Char, Int) = ...
    def help2(L: List[(Char, Int)], L2: List[Char]): List[(Char, Int)] = ...

### #6 Common Subexpressions

You should avoid unnecessary invocations of computation-intensive methods. For example

    this.remove(this.findMin).ascending(t + this.findMin)

invokes the `this.findMin` method twice. If each invocation is expensive (e.g. has to traverse an entire data structure) and does not have a side-effect, you can save one by introducing a local value binding:

    val min = this.findMin
    this.remove(min).ascending(t + min)

This becomes even more important if the function is invoked recursively: in this case the method is not only invoked multiple times, but an exponential number of times.

### #7 Don't Copy-Paste Code!

Copy-pasting code is always a warning sign for bad style!
There are many disadvantages:

- The code is longer, it takes more time to understand it
- If the two parts are not identical, but very similar, it is very difficult to spot the differences (see example below)
- Maintaining two copies and making sure that they remain synchronized is very error-prone
- The amount of work required to make changes to the code is multiplied

You should factor out common parts into separate methods instead of copying code around.
Example (see also #3 above for another example):

    val googleTweets: TweetSet = TweetReader.allTweets.filter(tweet =>
      google.exists(word => tweet.text.contains(word)))
    val appleTweets: TweetSet = TweetReader.allTweets.filter(tweet =>
      apple.exists(word => tweet.text.contains(word)))

This code is better written as follows:

    def tweetsMentioning(dictionary: List[String]): TweetSet =
      TweetReader.allTweets.filter(tweet =>
        dictionary.exists(word => tweet.text.contains(word)))

    val googleTweets = tweetsMentioning(google)
    val appleTweets  = tweetsMentioning(apple)

### #8 Scala doesn't require Semicolons

Semicolons in Scala are only required when writing multiple statements on the same line.
Writing unnecessary semicolons should be avoided, for example:

    def filter(p: Tweet => Boolean): TweetSet = filter0(p, new Empty);

### #9 Don't submit Code with "print" Statements

You should clean up your code and remove all `print` or `println` statements before submitting it.
The same will apply once you work for a company and create code that is used in production: the final code should be free of debugging statements.

### #10 Avoid using Return

In Scala, you often don't need to use explicit `return`s because control structures such as `if` are expressions.
For example, in

    def factorial(n: Int): Int =
      if (n <= 0) return 1
      else return (n * factorial(n-1))

the `return` statements can simply be dropped.

### #11 Avoid mutable local Variables

Since this is a course on functional programming, we want you to get used to writing code in a purely functional style, without using side-effecting operations.
You can often rewrite code that uses mutable local variables to code with helper functions that take accumulators.
Instead of:

    def fib(n: Int): Int =
      var a = 0
      var b = 1
      var i = 0
      while (i < n):
            val prev_a = a
            a = b
            b = prev_a + b
            i = i + 1
      a

prefer:

    def fib(n: Int): Int =
      def fibIter(i: Int, a: Int, b: Int): Int =
        if i == n then a
    else fibIter(i+1, b, a+b)

      fibIter(0, 0, 1)

### #12 Eliminate redundant "If" Expressions

Instead of

    if cond then true else false

you can simply write

    cond

(Similarly for the negative case).
