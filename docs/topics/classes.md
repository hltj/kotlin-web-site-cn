[//]: # (title: 类)

Kotlin 中使用关键字 `class` 声明类

```kotlin
class Person { /*……*/ }
```

类声明由类名、类头（指定其类型参数、主构造函数<!--
-->等）以及由花括号包围的类体构成。类头与类体都是可选的；
如果一个类没有类体，可以省略花括号。

```kotlin
class Empty
```

## 构造函数

在 Kotlin 中的一个类可以有一个*主构造函数*以及一个或多个*次构造函数*。主构造函数是类头的<!--
-->一部分：它跟在类名与可选的类型参数后。

```kotlin
class Person constructor(firstName: String) { /*……*/ }
```

如果主构造函数没有任何注解或者可见性修饰符，可以省略这个 `constructor` 关键字。

```kotlin
class Person(firstName: String) { /*……*/ }
```

主构造函数不能包含任何的代码。初始化的代码可以放<!--
-->到以 `init` 关键字作为前缀的*初始化块（initializer blocks）*中。

在实例初始化期间，初始化块按照它们出现在<!--
-->类体中的顺序执行，与属性初始化器交织在一起：

```kotlin
//sampleStart
class InitOrderDemo(name: String) {
    val firstProperty = "First property: $name".also(::println)
    
    init {
        println("First initializer block that prints $name")
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
{kotlin-runnable="true"}

主构造的参数可以在初始化块中使用。它们也可以在<!--
-->类体内声明的属性初始化器中使用：

```kotlin
class Customer(name: String) {
    val customerKey = name.uppercase()
}
```

Kotlin has a concise syntax for declaring properties and initializing them from the primary constructor:

```kotlin
class Person(val firstName: String, val lastName: String, var age: Int)
```

Such declarations can also include default values of the class properties:

```kotlin
class Person(val firstName: String, val lastName: String, var isEmployed: Boolean = true)
```

声明类属性时，可以使用[尾部逗号](coding-conventions.md#trailing-commas)：

```kotlin
class Person(
    val firstName: String,
    val lastName: String,
    var age: Int, // 尾部逗号
) { /*...*/ }
```

与普通属性一样，主构造函数中声明的属性可以是可变的（`var`）或只读的（`val`）。

如果构造函数有注解或可见性修饰符，这个 `constructor` 关键字是必需的，并且这些修饰符在它前面：

```kotlin
class Customer public @Inject constructor(name: String) { /*……*/ }
```

Learn more about [visibility modifiers](visibility-modifiers.md#构造函数).

### 次构造函数

类也可以声明前缀有 `constructor`的*次构造函数*：

```kotlin
class Person(val pets: MutableList<Pet> = mutableListOf())

class Pet {
    constructor(owner: Person) {
        owner.pets.add(this) // adds this pet to the list of its owner's pets
    }
}
```

如果类有一个主构造函数，每个次构造函数需要委托给主构造函数，
可以直接委托或者通过别的次构造函数间接委托。委托到同一个类的另一个构造函数<!--
-->用 `this` 关键字即可：

```kotlin
class Person(val name: String) {
    val children: MutableList<Person> = mutableListOf()
    constructor(name: String, parent: Person) : this(name) {
        parent.children.add(this)
    }
}
```

请注意，初始化块中的代码实际上会成为主构造函数的一部分。委托给主构造函数<!--
-->会作为次构造函数的第一条语句，因此所有初始化块与属性初始化器中的代码<!--
-->都会在次构造函数体之前执行。

即使该类没有主构造函数，这种委托仍会<!--
-->隐式发生，并且仍会执行初始化块：

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
{kotlin-runnable="true"}

如果一个非抽象类没有声明任何（主或次）构造函数，它会有一个生成的<!--
-->不带参数的主构造函数。构造函数的可见性是 public。

如果你不希望你的类有一个公有构造函数，那么声明一个带有非默认可见性的空的主构造函数：

```kotlin
class DontCreateMe private constructor() { /*……*/ }
```

> 在 JVM 上，如果主构造函数的所有的参数都有默认值，编译器会生成一个额外的无参构造函数，它将使用默认值。这使得 Kotlin 更易于使用像 Jackson 或者 JPA 这样的通过无参构造函数创建类的实例的库。
>
> ```kotlin
> class Customer(val customerName: String = "")
> ```
>
{type="note"}

## 创建类的实例

创建一个类的实例，只需像普通函数一样调用构造函数：

```kotlin
val invoice = Invoice()

val customer = Customer("Joe Smith")
```

> Kotlin does not have a `new` keyword.
>
{type="note"}

创建嵌套类、内部类与匿名内部类的类实例的过程在[嵌套类](nested-classes.md)中有述。

## 类成员

类可以包含：

* [构造函数与初始化块](classes.md#构造函数)
* [函数](functions.md)
* [属性](properties.md)
* [嵌套类与内部类](nested-classes.md)
* [对象声明](object-declarations.md)

## 继承

Classes can be derived from each other and form inheritance hierarchies.
[Learn more about inheritance in Kotlin](inheritance.md).

## 抽象类

类以及其中的某些或全部成员可以声明为 `abstract`。
抽象成员在本类中可以不用实现。
并不需要用 `open` 标注抽象类或者函数。

```kotlin
abstract class Polygon {
    abstract fun draw()
}

class Rectangle : Polygon() {
    override fun draw() {
        // draw the rectangle
    }
}
```

可以用一个抽象成员覆盖一个非抽象的开放成员。

```kotlin
open class Polygon {
    open fun draw() {
        // some default polygon drawing method
    }
}

abstract class WildShape : Polygon() {
    // Classes that inherit WildShape need to provide their own
    // draw method instead of using the default on Polygon
    abstract override fun draw()
}
```

## 伴生对象

如果你需要写一个可以无需用一个类的实例来调用、但需要访问类内部的<!--
-->函数（例如，工厂方法），你可以把它写成该类内[对象声明](object-declarations.md)中的一员。

更具体地讲，如果在你的类内声明了一个[伴生对象](object-declarations.md#伴生对象)，
你就可以访问其成员，只是以类名作为限定符。
