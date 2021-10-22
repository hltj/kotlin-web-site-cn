[//]: # (title: 密封类)

_Sealed_ classes and interfaces represent restricted class hierarchies that provide more control over inheritance.
All direct subclasses of a sealed class are known at compile time. No other subclasses may appear after
a module with the sealed class is compiled. For example, third-party clients can't extend your sealed class in their code.
Thus, each instance of a sealed class has a type from a limited set that is known when this class is compiled.

The same works for sealed interfaces and their implementations: once a module with a sealed interface is compiled,
no new implementations can appear.

In some sense, sealed classes are similar to [`enum`](enum-classes.md) classes: the set of values
for an enum type is also restricted, but each enum constant exists only as a _single instance_, whereas a subclass
of a sealed class can have _multiple_ instances, each with its own state.

As an example, consider a library's API. It's likely to contain error classes to let the library users handle errors 
that it can throw. If the hierarchy of such error classes includes interfaces or abstract classes visible in the public API,
then nothing prevents implementing or extending them in the client code. However, the library doesn't know about errors
declared outside it, so it can't treat them consistently with its own classes. With a sealed hierarchy of error classes,
library authors can be sure that they know all possible error types and no other ones can appear later.

To declare a sealed class or interface, put the `sealed` modifier before its name:

```kotlin
sealed interface Error

sealed class IOError(): Error

class FileReadError(val f: File): IOError()
class DatabaseError(val source: DataSource): IOError()

object RuntimeError : Error
```

一个密封类是自身[抽象的](classes.md#抽象类)，它不能直接实例化并可以有抽象（`abstract`）成员。

Constructors of sealed classes can have one of two [visibilities](visibility-modifiers.md): `protected` (by default) or
`private`:

```kotlin
sealed class IOError {
    constructor() { /*...*/ } // protected by default
    private constructor(description: String): this() { /*...*/ } // private is OK
    // public constructor(code: Int): this() {} // Error: public and internal are not allowed
}
```

## Location of direct subclasses

Direct subclasses of sealed classes and interfaces must be declared in the same package. They may be top-level or nested
inside any number of other named classes, named interfaces, or named objects. Subclasses can have any [visibility](visibility-modifiers.md)
as long as they are compatible with normal inheritance rules in Kotlin.

Subclasses of sealed classes must have a proper qualified name. They can't be local nor anonymous objects.

> `enum` classes can't extend a sealed class (as well as any other class), but they can implement sealed interfaces.
>
{type="note"}

These restrictions don't apply to indirect subclasses. If a direct subclass of a sealed class is not marked as sealed,
it can be extended in any ways that its modifiers allow:

```kotlin
sealed interface Error // has implementations only in same package and module

sealed class IOError(): Error // extended only in same package and module
open class CustomError(): Error // can be extended wherever it's visible
```

### Inheritance in multiplatform projects

There is one more inheritance restriction in [multiplatform projects](mpp-intro.md): direct subclasses of sealed classes must
reside in the same source set. It applies to sealed classes without the [`expect` and `actual` modifiers](mpp-connect-to-apis.md).

If a sealed class is declared as `expect` in a common source set and have `actual` implementations in platform source sets,
both `expect` and `actual` versions can have subclasses in their source sets. Moreover, if you use a [hierarchical structure](mpp-share-on-platforms.md#对相似平台共享代码),
you can create subclasses in any source set between the `expect` and `actual` declarations. 

[Learn more about the hierarchical structure of multiplatform projects](mpp-share-on-platforms.md#对相似平台共享代码). 

## Sealed classes and when expression

使用密封类的关键好处在于使用 [`when` 表达式](control-flow.md#when-表达式)<!--
-->的时候。
如果能够验证语句覆盖了所有情况，就不需要为该语句再添加一个 `else` 子句了。 
当然，这只有当你用 `when` 作为表达式（使用结果）而不是作为语句时才有用。

```kotlin
fun log(e: Error) = when(e) {
    is FileReadError -> { println("Error while reading file ${e.file}") }
    is DatabaseError -> { println("Error while reading from database ${e.source}") }
    RuntimeError ->  { println("Runtime error") }
    // 不再需要 `else` 子句，因为已经覆盖了所有的情况
}
```

> `when` expressions on [`expect`](mpp-connect-to-apis.md) sealed classes in the common code of multiplatform projects still 
> require an `else` branch. This happens because subclasses of `actual` platform implementations aren't known in the 
> common code.
>
{type="note"}
