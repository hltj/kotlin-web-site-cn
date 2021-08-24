[//]: # (title: 内联类)

> Inline classes are in [Beta](components-stability.md). They are almost stable, but migration steps may be required in the future. 
> We'll do our best to minimize any changes you will have to make.
> We would appreciate your feedback on the inline classes feature in [YouTrack](https://youtrack.jetbrains.com/issue/KT-42434).
>
{type="warning"}

有时候，业务逻辑需要围绕某种类型创建包装器。然而，由于额外的堆内存分配问题，
它会引入运行时的性能开销。此外，如果被包装的类型是原生类型，性能的损失是很糟糕的，
因为原生类型通常在运行时就进行了大量优化，然而他们的包装器却没有得到任何特殊的处理。 

为了解决这类问题，Kotlin 引入了一种被称为*内联类*的特殊类。
Inline classes are a subset of value-based classes. They don't have an identity and can only hold values.

To declare an inline class, use an `inline` or `value` modifier before the name of the class:

```kotlin
inline class Password(val value: String)
```

```kotlin
value class Password(private val s: String)
```

To declare an inline class for the JVM backend, use the `value` modifier along with the `@JvmInline` annotation before the class declaration: 

```kotlin
// For JVM backends
@JvmInline
value class Password(private val s: String)
```

内联类必须含有唯一的一个属性在主构造函数中初始化。在运行时，
将使用这个唯一属性来表示内联类的实例（关于运行时的内部表达请参阅[下文](#表示方式)）：

```kotlin
// 不存在 'Password' 类的真实实例对象
// 在运行时，'securePassword' 仅仅包含 'String'
val securePassword = Password("Don't try this in production") 
```

这就是内联类的主要特性，它灵感来源于 *inline* 这个名称：类的数据被*内联*到该类<!--
-->使用的地方（类似于[内联函数](inline-functions.md)中的代码被内联到该函数调用的地方）。

## 成员

内联类支持普通类中的一些功能。特别是，内联类可以声明属性与函数,
and have the `init` block：

```kotlin
@JvmInline
value class Name(val s: String) {
    init {
        require(s.length > 0) { }
    }

    val length: Int
        get() = s.length

    fun greet() {
        println("Hello, $s")
    }
}

fun main() {
    val name = Name("Kotlin")
    name.greet() // `greet` 方法会作为一个静态方法被调用
    println(name.length) // 属性的 get 方法会作为一个静态方法被调用
}
```

There are some restrictions for inline class members:
* Inline class properties cannot have [backing fields](properties.md#backing-fields). They can only have simple computable 
properties (no `lateinit`/delegated properties).
* Inline classes cannot have `var` properties or extension `var` properties.

## 继承

内联类允许去继承接口

```kotlin
interface Printable {
    fun prettyPrint(): String
}

@JvmInline
value class Name(val s: String) : Printable {
    override fun prettyPrint(): String = "Let's $s!"
}

fun main() {
    val name = Name("Kotlin")
    println(name.prettyPrint()) // 仍然会作为一个静态方法被调用
}
```

禁止内联类参与到类的继承关系结构中。这就意味着内联类不能继承<!--
-->其他的类而且必须是 `final`。

## 表示方式

在生成的代码中，Kotlin 编译器为每个内联类保留一个包装器。内联类的实例可以在<!--
-->运行时表示为包装器或者基础类型。这就类似于 `Int`
可以[表示](basic-types.md#numbers-representation-on-the-jvm)为原生类型 `int` 或者包装器 `Integer`。

为了生成性能最优的代码，Kotlin 编译更倾向于使用基础类型而不是包装器。
然而，有时候使用包装器是必要的。一般来说，只要将内联类用作另一种类型，
它们就会被装箱。

```kotlin
interface I

@JvmInline
value class Foo(val i: Int) : I

fun asInline(f: Foo) {}
fun <T> asGeneric(x: T) {}
fun asInterface(i: I) {}
fun asNullable(i: Foo?) {}

fun <T> id(x: T): T = x

fun main() {
    val f = Foo(42) 
    
    asInline(f)    // 拆箱操作: 用作 Foo 本身
    asGeneric(f)   // 装箱操作: 用作泛型类型 T
    asInterface(f) // 装箱操作: 用作类型 I
    asNullable(f)  // 装箱操作: 用作不同于 Foo 的可空类型 Foo?
    
    // 在下面这里例子中，'f' 首先会被装箱（当它作为参数传递给 'id' 函数时）然后又被拆箱（当它从'id'函数中被返回时）
    // 最后， 'c' 中就包含了被拆箱后的内部表达(也就是 '42')， 和 'f' 一样
    val c = id(f)  
}
```

因为内联类既可以表示为基础类型有可以表示为包装器，[引用相等](equality.md#引用相等)<!--
-->对于内联类而言毫无意义，因此这也是被禁止的。

### 名字修饰

由于内联类被编译为其基础类型，因此可能会导致各种模糊的错误，例如意想不到的平台签名冲突：

```kotlin
@JvmInline
value class UInt(val x: Int)

// 在 JVM 平台上被表示为'public final void compute(int x)'
fun compute(x: Int) { }

// 同理，在 JVM 平台上也被表示为'public final void compute(int x)'！
fun compute(x: UInt) { }
```

为了缓解这种问题，一般会通过在函数名后面拼接一些稳定的哈希码来重命名函数。
因此，`fun compute(x: UInt)` 将会被表示为 `public final void compute-<hashcode>(int x)`，以此来解决冲突的问题。

> The mangling scheme has been changed in Kotlin 1.4.30. 
> Use the `-Xuse-14-inline-classes-mangling-scheme` compiler flag to force the compiler to use the old 1.4.0 mangling scheme and preserve binary compatibility.
>
{type="note"}

### Calling from Java code

You can call functions that accept inline classes from Java code. To do so, you should manually disable mangling:
add the `@JvmName` annotation before the function declaration:

```kotlin
@JvmInline
value class UInt(val x: Int)

fun compute(x: Int) { }

@JvmName("computeUInt")
fun compute(x: UInt) { }
```

## 内联类与类型别名

初看起来，内联类似乎与[类型别名](type-aliases.md)非常相似。实际上，两者似乎都引入了<!--
-->一种新的类型，并且都在运行时表示为基础类型。

然而，关键的区别在于类型别名与其基础类型（以及具<!--
-->有相同基础类型的其他类型别名）是*赋值兼容*的，而内联类却不是这样。

换句话说，内联类引入了一个真实的新类型，与类型别名正好相反，类型别名仅仅是为现有的类型取了个新的替代名称
（别名）：

```kotlin
typealias NameTypeAlias = String

@JvmInline
value class NameInlineClass(val s: String)

fun acceptString(s: String) {}
fun acceptNameTypeAlias(n: NameTypeAlias) {}
fun acceptNameInlineClass(p: NameInlineClass) {}

fun main() {
    val nameAlias: NameTypeAlias = ""
    val nameInlineClass: NameInlineClass = NameInlineClass("")
    val string: String = ""

    acceptString(nameAlias) // 正确: 传递别名类型的实参替代函数中基础类型的形参
    acceptString(nameInlineClass) // 错误: 不能传递内联类的实参替代函数中基础类型的形参

    // And vice versa:
    acceptNameTypeAlias(string) // 正确: 传递基础类型的实参替代函数中别名类型的形参
    acceptNameInlineClass(string) // 错误: 不能传递基础类型的实参替代函数中内联类类型的形参
}
```

## 启用内联类
 
When using inline classes, a warning will be reported, indicating that this feature has not been released as stable.
To remove the warning you have to opt in to the usage of this feature by passing the compiler argument `-Xinline-classes`.

### Gradle

<tabs>
<tab title="Groovy">

```groovy
kotlin {
    sourceSets.all {
        languageSettings.enableLanguageFeature('InlineClasses')
    }
}
```

</tab>
<tab title="Kotlin">

```kotlin
kotlin {
    sourceSets.all {
        languageSettings.enableLanguageFeature("InlineClasses")
    }
}
```

</tab>
</tabs>

关于详细信息，请参见[编译器选项](gradle.md#编译器选项)。关于[多平台项目](mpp-intro.md)的设置，请参见[使用 Gradle 构建多平台项目](building-mpp-with-gradle.md#语言设置)章节。
参见[语言设置](mpp-dsl-reference.md#language-settings)。

### Maven

```xml
<configuration>
    <args>
        <arg>-Xinline-classes</arg> 
    </args>
</configuration>
```

关于详细信息，请参见[指定编译器选项](maven.md#指定编译器选项)。

## 进一步讨论

关于其他技术详细信息和讨论，
请参见[内联类的语言提议](https://github.com/Kotlin/KEEP/blob/master/proposals/inline-classes.md)
