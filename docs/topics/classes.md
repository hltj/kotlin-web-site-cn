## Classes

Classes in Kotlin are declared using the keyword `class`:

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
class Invoice { /*...*/ }
```

</div>

The class declaration consists of the class name, the class header (specifying its type parameters, the primary
constructor etc.) and the class body, surrounded by curly braces. Both the header and the body are optional;
if the class has no body, curly braces can be omitted.

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
class Empty
```

</div>

### Constructors

A class in Kotlin can have a **primary constructor** and one or more **secondary constructors**. The primary
constructor is part of the class header: it goes after the class name (and optional type parameters).

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
class Person constructor(firstName: String) { /*...*/ }
```

</div>

If the primary constructor does not have any annotations or visibility modifiers, the `constructor`
keyword can be omitted:

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
class Person(firstName: String) { /*...*/ }
```

</div>

The primary constructor cannot contain any code. Initialization code can be placed
in **initializer blocks**, which are prefixed with the `init` keyword.

During an instance initialization, the initializer blocks are executed in the same order as they appear
in the class body, interleaved with the property initializers:

<div class="sample" markdown="1" theme="idea">

```kotlin
//sampleStart
class InitOrderDemo(name: String) {
    val firstProperty = "First property: $name".also(::println)
    
    init {
        println("First initializer block that prints ${name}")
    }
    
    val secondProperty = "Second property: ${name.length}".also(::println)
    
    init {
        println("Second initializer block that prints ${name.length}")
    }
}
//sampleEnd

fun main() {
    InitOrderDemo("hello")
}
```

</div>

Note that parameters of the primary constructor can be used in the initializer blocks. They can also be used in
property initializers declared in the class body:

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
class Customer(name: String) {
    val customerKey = name.toUpperCase()
}
```

</div>

In fact, for declaring properties and initializing them from the primary constructor, Kotlin has a concise syntax:

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
class Person(val firstName: String, val lastName: String, var age: Int) { /*...*/ }
```

</div>

You can use a [trailing comma](coding-conventions.md#trailing-commas) when you declare class properties:

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
class Person(
    val firstName: String,
    val lastName: String,
    var age: Int, // trailing comma
) { /*...*/ }
```

</div>

Much the same way as regular properties, the properties declared in the primary constructor can be
mutable (`var`) or read-only (`val`).

If the constructor has annotations or visibility modifiers, the `constructor` keyword is required, and
the modifiers go before it:

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
class Customer public @Inject constructor(name: String) { /*...*/ }
```

</div>

For more details, see [Visibility Modifiers](visibility-modifiers.md#constructors).

#### Secondary constructors

The class can also declare **secondary constructors**, which are prefixed with `constructor`:

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
class Person {
    var children: MutableList<Person> = mutableListOf()
    constructor(parent: Person) {
        parent.children.add(this)
    }
}
```

</div>

If the class has a primary constructor, each secondary constructor needs to delegate to the primary constructor, either
directly or indirectly through another secondary constructor(s). Delegation to another constructor of the same class
is done using the `this` keyword:

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
class Person(val name: String) {
    var children: MutableList<Person> = mutableListOf()
    constructor(name: String, parent: Person) : this(name) {
        parent.children.add(this)
    }
}
```

</div>

Note that code in initializer blocks effectively becomes part of the primary constructor. Delegation to the primary
constructor happens as the first statement of a secondary constructor, so the code in all initializer blocks and property initializers is executed
before the secondary constructor body. Even if the class has no primary constructor, the delegation still happens
implicitly, and the initializer blocks are still executed:

<div class="sample" markdown="1" theme="idea">

```kotlin
//sampleStart
class Constructors {
    init {
        println("Init block")
    }

    constructor(i: Int) {
        println("Constructor $i")
    }
}
//sampleEnd

fun main() {
    Constructors(1)
}
```

</div>

If a non-abstract class does not declare any constructors (primary or secondary), it will have a generated primary
constructor with no arguments. The visibility of the constructor will be public. If you do not want your class
to have a public constructor, you need to declare an empty primary constructor with non-default visibility:

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
class DontCreateMe private constructor () { /*...*/ }
```

</div>

> **NOTE**: On the JVM, if all of the parameters of the primary constructor have default values, the compiler will
> generate an additional parameterless constructor which will use the default values. This makes it easier to use
> Kotlin with libraries such as Jackson or JPA that create class instances through parameterless constructors.

><div class="sample" markdown="1" theme="idea" data-highlight-only>
>
>```kotlin
>class Customer(val customerName: String = "")
>```
>
></div>

{:.info}

### Creating instances of classes

To create an instance of a class, we call the constructor as if it were a regular function:

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
val invoice = Invoice()

val customer = Customer("Joe Smith")
```

</div>

Note that Kotlin does not have a `new` keyword.

Creating instances of nested, inner and anonymous inner classes is described in [Nested classes](nested-classes.html).

### Class members

Classes can contain:

* [Constructors and initializer blocks](classes.md#constructors)
* [Functions](functions.html)
* [Properties](properties.html)
* [Nested and Inner Classes](nested-classes.html)
* [Object Declarations](object-declarations.html)

## Inheritance

Classes can be derived from each other and form inheritance hierarchies.
[Learn more about inheritance in Kotlin](inheritance.md).

## Abstract classes

A class and some of its members may be declared `abstract`.
An abstract member does not have an implementation in its class.
Note that we do not need to annotate an abstract class or function with open – it goes without saying.

We can override a non-abstract open member with an abstract one

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
open class Polygon {
    open fun draw() {}
}

abstract class Rectangle : Polygon() {
    abstract override fun draw()
}
```

</div>

## Companion objects

If you need to write a function that can be called without having a class instance but needs access to the internals
of a class (for example, a factory method), you can write it as a member of an [object declaration](object-declarations.html)
inside that class.

Even more specifically, if you declare a [companion object](object-declarations.md#companion-objects) inside your class,
you can access its members using only the class name as a qualifier.
