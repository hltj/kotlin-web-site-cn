## 类

Kotlin 中使用关键字 *class*{:.keyword} 声明类

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
class Invoice { /*……*/ }
```

</div>

类声明由类名、类头（指定其类型参数、主<!--
-->构造函数等）以及由花括号包围的类体构成。类头与类体都是可选的；
如果一个类没有类体，可以省略花括号。

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
class Empty
```

</div>

### 构造函数

在 Kotlin 中的一个类可以有一个**主构造函数**以及一个或多个**次构造函数**。主<!--
-->构造函数是类头的一部分：它跟在类名（与可选的类型参数）后。

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
class Person constructor(firstName: String) { /*……*/ }
```

</div>

如果主构造函数没有任何注解或者可见性修饰符，可以省略这个 `constructor`
关键字。

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
class Person(firstName: String) { /*……*/ }
```

</div>

主构造函数不能包含任何的代码。初始化的代码可以放<!--
-->到以 *init*{:.keyword} 关键字作为前缀的**初始化块（initializer blocks）**中。

在实例初始化期间，初始化块按照它们出现在<!--
-->类体中的顺序执行，与属性初始化器交织在一起：

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

请注意，主构造的参数可以在初始化块中使用。它们也可以在<!--
-->类体内声明的属性初始化器中使用：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
class Customer(name: String) {
    val customerKey = name.toUpperCase()
}
```

</div>

事实上，声明属性以及从主构造函数初始化属性，Kotlin 有简洁的语法：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
class Person(val firstName: String, val lastName: String, var age: Int) { /*……*/ }
```

</div>

声明类属性时，可以使用[尾部逗号](coding-conventions.md#trailing-commas)：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
class Person(
    val firstName: String,
    val lastName: String,
    var age: Int, // 尾部逗号
) { /*...*/ }
```

</div>

与普通属性一样，主构造函数中声明的属性可以是<!--
-->可变的（`var`）或只读的（`val`）。

如果构造函数有注解或可见性修饰符，这个 `constructor` 关键字是必需的，并且<!--
-->这些修饰符在它前面：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
class Customer public @Inject constructor(name: String) { /*……*/ }
```

</div>

更多详情，参见[可见性修饰符](visibility-modifiers.md#构造函数)

#### 次构造函数

类也可以声明前缀有 `constructor`的**次构造函数**：

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

如果类有一个主构造函数，每个次构造函数需要委托给主构造函数，
可以直接委托或者通过别的次构造函数间接委托。委托到同一个类的另一个构造函数<!--
-->用 `this` 关键字即可：

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

请注意，初始化块中的代码实际上会成为主构造函数的一部分。委托给主<!--
-->构造函数会作为次构造函数的第一条语句，因此所有初始化块与属性初始化器中的代码都会<!--
-->在次构造函数体之前执行。即使该类没有主构造函数，这种委托仍会<!--
-->隐式发生，并且仍会执行初始化块：

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

如果一个非抽象类没有声明任何（主或次）构造函数，它会有一个生成的<!--
-->不带参数的主构造函数。构造函数的可见性是 public。如果你不希望你的类<!--
-->有一个公有构造函数，你需要声明一个带有非默认可见性的空的主构造函数：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
class DontCreateMe private constructor () { /*……*/ }
```

</div>

> **注意**：在 JVM 上，如果主构造函数的所有的参数都有默认值，编译器会生成
> 一个额外的无参构造函数，它将使用默认值。这使得
> Kotlin 更易于使用像 Jackson 或者 JPA 这样的通过无参构造函数创建类的实例的库。

><div class="sample" markdown="1" theme="idea" data-highlight-only>
>
>```kotlin
>class Customer(val customerName: String = "")
>```
>
></div>

{:.info}

### 创建类的实例

要创建一个类的实例，我们就像普通函数一样调用构造函数：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
val invoice = Invoice()

val customer = Customer("Joe Smith")
```

</div>

注意 Kotlin 并没有 `new` 关键字。

创建嵌套类、内部类与匿名内部类的类实例在[嵌套类](nested-classes.html)中有述。

### 类成员

类可以包含：

* [构造函数与初始化块](classes.md#构造函数)
* [函数](functions.html)
* [属性](properties.html)
* [嵌套类与内部类](nested-classes.html)
* [对象声明](object-declarations.html)

## Inheritance

Classes can be derived from each other and form inheritance hierarchies.
[Learn more about inheritance in Kotlin](inheritance.md).

## 抽象类

类以及其中的某些成员可以声明为 *abstract*{: .keyword}。
抽象成员在本类中可以不用实现。
需要注意的是，我们并不需要用 `open` 标注一个抽象类或者函数——因为这不言而喻。

我们可以用一个抽象成员覆盖一个非抽象的开放成员

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

## 伴生对象

如果你需要写一个可以无需用一个类的实例来调用、但需要访问类内部的<!--
-->函数（例如，工厂方法），你可以把它写成该类内[对象声明](object-declarations.html)<!--
-->中的一员。

更具体地讲，如果在你的类内声明了一个[伴生对象](object-declarations.md#伴生对象)，
你就可以访问其成员，只是以类名作为限定符。
