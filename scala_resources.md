# Scala resources

## What is Scala?

Scala is a modern multi-paradigm programming language designed to express common programming patterns in a concise, elegant, and type-safe way. It seamlessly integrates features of object-oriented and functional languages.

## Scala is object-oriented

Scala is a pure object-oriented language in the sense that every value is an object. Types and behaviors of objects are described by classes and traits. Classes can be extended by subclassing, and by using a flexible mixin-based composition mechanism as a clean replacement for multiple inheritance.

## Scala is functional

Scala is also a functional language in the sense that every function is a value. Scala provides a lightweight syntax for defining anonymous functions, it supports higher-order functions, it allows functions to be nested, and it supports currying. Scala’s case classes and its built-in support for pattern matching provide the functionality of algebraic types, which are used in many functional languages. Singleton objects provide a convenient way to group functions that aren’t members of a class.

Furthermore, Scala’s notion of pattern matching naturally extends to the processing of XML data with the help of right-ignoring sequence patterns, by way of general extension via extractor objects. In this context, for comprehensions are useful for formulating queries. These features make Scala ideal for developing applications like web services.

## setup

Install coursier

- https://get-coursier.io/docs/cli-installation.html

```bash
brew install coursier/formulas/coursier
cs setup
```

Setup with coursier

The following command downloads and installs the Scala Build Tool (sbt) as well as Adopt OpenJDK 11, the Java Development Kit version 11, which is required for the assignments:

```bash
cs setup --jvm adopt:11
```

## resources

- [Visual Scala Reference](https://superruzafa.github.io/visual-scala-reference/)
- [Scala 3 Book](https://docs.scala-lang.org/scala3/book/introduction.html)
- [Scala Exercises](https://www.scala-exercises.org/)
- [Glossary | Scala Documentation](https://docs.scala-lang.org/glossary/)
- [Introduction to Programming and Problem Solving Using Scala - YouTube](https://www.youtube.com/playlist?list=PLLMXbkbDbVt9MIJ9DV4ps-_trOzWtphYO)
