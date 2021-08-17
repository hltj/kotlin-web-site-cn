---
type: doc
layout: reference
category: "Classes and Objects"
title: "密封类"
---

# 密封类

_Sealed_ classes represent restricted class hierarchies that provide more control over inheritance.
All subclasses of a sealed class are known at compile time. No other subclasses may appear after
a module with the sealed class is compiled. For example, third-party clients can't extend your sealed class in their code.
Thus, each instance of a sealed class has a type from a limited set that is known when this class is compiled. 

In some sense, sealed classes are similar to enum classes: the set of values
for an enum type is also restricted, but each enum constant exists only as a _single instance_, whereas a subclass
of a sealed class can have _multiple_ instances, each with its own state.

To declare a sealed class, put the `sealed` modifier before its name.



```kotlin
sealed class Expr

data class Const(val number: Double) : Expr()
data class Sum(val e1: Expr, val e2: Expr) : Expr()
object NotANumber : Expr()
```


一个密封类是自身[抽象的](classes.html#抽象类)，它不能直接实例化并可以有抽象（*abstract*{: .keyword }）成员。

密封类不允许有非-*private*{: .keyword } 构造函数（其构造函数默认为 *private*{: .keyword }）。

## Sealed interfaces

> Sealed interfaces are [Experimental](evolution/components-stability.html). They may be dropped or changed at any time.
> Opt-in is required (see details [below](#try-sealed-interfaces-and-package-wide-hierarchies-of-sealed-classes)). Use them only for evaluation purposes. We would appreciate your feedback on them in [YouTrack](https://youtrack.jetbrains.com/issues/KT-42433).
{:.note}

Interfaces can be declared `sealed` as well as classes. The `sealed` modifier works on interfaces the same way:
all implementations of a sealed interface are known at compile time. Once a module with a sealed interface is compiled,
no new implementations can appear.



```kotlin
sealed interface Expr

sealed class MathExpr(): Expr

data class Const(val number: Double) : MathExpr()
data class Sum(val e1: Expr, val e2: Expr) : MathExpr()
object NotANumber : Expr
```


## Location of direct subclasses

All direct subclasses of a sealed class must be declared in the same file as this class itself. Classes that extend
direct subclasses of a sealed class (indirect inheritors) can be placed anywhere, not necessarily in the same file.

### Additional location: the same package

> Package-wide hierarchies of sealed classes are [Experimental](evolution/components-stability.html). They may be dropped or changed at any time.
> Opt-in is required (see details [below](#try-sealed-interfaces-and-package-wide-hierarchies-of-sealed-classes)). Use them only for evaluation purposes. We would appreciate your feedback on them in [YouTrack](https://youtrack.jetbrains.com/issues/KT-42433).
{:.note}

Direct subclasses of sealed classes and interfaces must be declared in the same package. They may be top-level or nested 
inside any number of other named classes, named interfaces, or named objects. Subclasses can have any [visibility](visibility-modifiers.html)
as long as they are compatible with normal inheritance rules in Kotlin.

Subclasses of sealed classes must have a proper qualified name. They can't be local nor anonymous objects.

> `enum` classes can't extend a sealed class (as well as any other class), but they can implement sealed interfaces.
{:.note}

## Sealed classes and when expression

使用密封类的关键好处在于使用 [`when` 表达式](control-flow.html#when-表达式) 的时候，如果能够<!--
-->验证语句覆盖了所有情况，就不需要为该语句再添加一个 `else` 子句了。
当然，这只有当你用 `when` 作为表达式（使用结果）而不是作为语句时才有用。



```kotlin
fun eval(expr: Expr): Double = when(expr) {
    is Const -> expr.number
    is Sum -> eval(expr.e1) + eval(expr.e2)
    NotANumber -> Double.NaN
    // 不再需要 `else` 子句，因为我们已经覆盖了所有的情况
}
```


## Try sealed interfaces and package-wide hierarchies of sealed classes

[Sealed interfaces](#sealed-interfaces) and [package-wide hierarchies](#additional-location-the-same-package) are [Experimental](evolution/components-stability.html).
To be able to use them in your code, switch to the language version `1.5`:
* In Gradle, add the [compiler option](using-gradle.html#attributes-common-for-jvm-and-js) `languageVersion` with the value `1.5`.

> Groovy DSL

```groovy
kotlinOptions.languageVersion = "1.5"
```

  

* In the command-line compiler, add the option `-language-version 1.5`.
