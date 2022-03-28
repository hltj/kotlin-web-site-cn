[//]: # (title: Kotlin 1.3 的新特性)

_发布日期：2018-10-29_

## 协程正式发布

历经了漫长而充足的的测试，协程终于正式发布了！这意味着自 Kotlin 1.3 起，协程的语言<!--
-->支持与 API 已[完全稳定](components-stability.md)。参见新版[协程概述](coroutines-overview.md)。

Kotlin 1.3 引入了挂起函数的可调用引用以及在反射 API 中对协程的支持。

## Kotlin/Native

Kotlin 1.3 继续改进与完善原生平台。详情请参见 [Kotlin/Native 概述](native-overview.md)。

## 多平台项目

在 1.3 中，我们完全修改了多平台项目的模型，以提高表现力与灵活性，
并使共享公共代码更加容易。此外，多平台项目现在也支持 Kotlin/Native！

与旧版模型的主要区别在于：

  * 在旧版模型中，需要将公共代码与平台相关代码分别放在独立的模块中，以 `expectedBy` 依赖项链接。
    现在，公共代码与平台相关代码放在相同模块的不同源根（source root）中，使项目更易于配置。
  * 对于不同的已支持平台，现在有大量的[预设的平台配置](multiplatform-dsl-reference.md#目标)。
  * 更改了[依赖配置]((multiplatform-add-dependencies.md))；
    现在为每个源根分别指定依赖项。
  * 源代码集（source set）现在可以在任意平台子集之间共享
  （例如，在一个目标平台为 JS、Android 与 iOS 的模块中，可以有一个只在 Android 与 iOS 之间共享的源代码集）。
  * 现在支持[发布多平台库](multiplatform-publish-lib.md)了。

更多相关信息，请参考[多平台程序设计文档](multiplatform.md)。

## 契约

Kotlin 编译器会做大量的静态分析工作，以提供警告并减少模版代码。其中最显著的特性之一<!--
-->就是智能转换——能够根据类型检测自动转换类型。

```kotlin
fun foo(s: String?) {
    if (s != null) s.length // 编译器自动将“s”转换为“String”
}
```

然而，一旦将这些检测提取到单独的函数中，所有智能转换都立即消失了：

```kotlin
fun String?.isNotNull(): Boolean = this != null

fun foo(s: String?) {
    if (s.isNotNull()) s.length // 没有智能转换 :(
}
```

为了改善在此类场景中的行为，Kotlin 1.3 引入了称为 *契约* 的实验性机制。

*契约* 让一个函数能够以编译器理解的方式显式描述其行为。
目前支持两大类场景：

- 通过声明函数调用的结果与所传参数值之间的关系来改进智能转换分析：

```kotlin
fun require(condition: Boolean) {
    // 这是一种语法格式，告诉编译器：
    // “如果这个函数成功返回，那么传入的‘condition’为 true”
    contract { returns() implies condition }
    if (!condition) throw IllegalArgumentException(...)
}

fun foo(s: String?) {
    require(s is String)
    // s 在这里智能转换为“String”，因为否则
    // “require”会抛出异常
}
```

-  在存在高阶函数的情况下改进变量初始化的分析：

```kotlin
fun synchronize(lock: Any?, block: () -> Unit) {
    // 告诉编译器：
    // “这个函数会在此时此处调用‘block’，并且刚好只调用一次”
    contract { callsInPlace(block, EXACTLY_ONCE) }
}

fun foo() {
    val x: Int
    synchronize(lock) {
        x = 42 // 编译器知道传给“synchronize”的 lambda 表达式刚好
               // 只调了一次，因此不会报重复赋值错
    }
    println(x) // 编译器知道一定会调用该 lambda 表达式而执行
               // 初始化操作，因此可以认为“x”在这里已初始化
}
```

### 标准库中的契约

`stdlib`（kotlin 标准库）已经利用契约带来了如上所述的对代码分析的改进。
这部分契约是**稳定版的**，这意味着你现在就可以从改进的代码分析中受益，而无需任何额外的 opt-ins：

```kotlin
//sampleStart
fun bar(x: String?) {
    if (!x.isNullOrEmpty()) {
        println("length of '$x' is ${x.length}") // 哇，已经智能转换为非空！
    }
}
//sampleEnd
fun main() {
    bar(null)
    bar("42")
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

### 自定义契约

可以为自己的函数声明契约，不过这个特性是**实验性的**，因为目前的语法<!--
-->尚处于早期原型状态，并且很可能还会更改。另外还要注意，目前 Kotlin 编译器并不会<!--
-->验证契约，因此程序员有责任编写正确合理的契约。

通过调用标准库（stdlib）函数 `contract` 来引入自定义契约，该函数提供了 DSL 作用域：

```kotlin
fun String?.isNullOrEmpty(): Boolean {
    contract {
        returns(false) implies (this@isNullOrEmpty != null)
    }
    return this == null || isEmpty()
}
```

请参见 [KEEP](https://github.com/Kotlin/KEEP/blob/master/proposals/kotlin-contracts.md) 中关于语法与兼容性注意事项的详细信息。

## 将 `when` 主语捕获到变量中

在 Kotlin 1.3 中，可以将 `when` 表达式主语捕获到一个变量中：

```kotlin
fun Request.getBody() =
        when (val response = executeRequest()) {
            is Success -> response.body
            is HttpError -> throw HttpException(response.status)
        }
```

虽然已经可以在 `when` 表达式前面提取这个变量，但是在 `when` 中的 `val` 使其作用域刚好限制在
`when` 主体中，从而防止命名空间污染。[关于 `when` 表达式的完整文档请参见这里](control-flow.md#when-表达式)。

## 接口中伴生对象的 @JvmStatic 与 @JvmField

对于 Kotlin 1.3，可以使用注解 `@JvmStatic` 与 `@JvmField` 标记接口的 `companion` 对象成员。
在类文件中会将这些成员提升到相应接口中并标记为 `static`。

例如，以下 Kotlin 代码：

```kotlin
interface Foo {
    companion object {
        @JvmField
        val answer: Int = 42

        @JvmStatic
        fun sayHello() {
            println("Hello, world!")
        }
    }
}
```

相当于这段 Java 代码：

```java
interface Foo {
    public static int answer = 42;
    public static void sayHello() {
        // ……
    }
}
```

## 注解类中的内嵌声明

在 Kotlin 1.3 中，注解可以有内嵌的类、接口、对象与伴生对象：

```kotlin
annotation class Foo {
    enum class Direction { UP, DOWN, LEFT, RIGHT }
    
    annotation class Bar

    companion object {
        fun foo(): Int = 42
        val bar: Int = 42
    }
}
```

## 无参的 `main`

按照惯例，Kotlin 程序的入口点是一个签名类似于 `main(args: Array<String>)` 的函数，
其中 `args` 表示传给该程序的命令行参数。然而，并非每个应用程序都支持命令行参数，
因此这个参数往往到最后都没有用到。 

Kotlin 1.3 引入了一种更简单的无参 `main` 形式。现在 Kotlin 版的 `Hello,World` 缩短了19个字符！

```kotlin
fun main() {
    println("Hello, world!")
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

## 更多元的函数

在 Kotlin 中用带有不同数量参数的泛型类来表示函数类型：`Function0<R>`、
`Function1<P0, R>`、 `Function2<P0, P1, R>`……这种方式有一个问题是这个列表是有限的，目前只到 `Function22`。 

Kotlin 1.3 放宽了这一限制，并添加了对具有更多元数（参数个数）的函数的支持：

```kotlin
fun trueEnterpriseComesToKotlin(block: (Any, Any, …… /* 42 个 */, Any) -> Any) {
    block(Any(), Any(), ……, Any())
}
```

## 渐进模式

Kotlin 非常注重代码的稳定性与向后兼容性：Kotlin 兼容性策略提到破坏性变更
（例如，会使以前编译正常的代码现在不能通过编译的变更）只能在主版本（**1.2**、**1.3** 等）中引入。

我们相信很多用户可以使用更快的周期，其中关键的编译器修复会即时生效，
从而使代码更安全、更正确。因此 Kotlin 1.3 引入了*渐进*编译器模式，可以<!--
-->通过将参数 `-progressive` 传给编译器来启用。

在渐进模式下，语言语义中的一些修复可以即时生效。所有这些修复都有以下两个重要特征：

* 保留了源代码与旧版编译器的向后兼容性，这意味着可以<!--
-->通过非渐进式编译器编译所有可由渐进式编译器编译的代码。
* 只是在某种意义上使代码*更安全*——例如，可以禁用某些不安全的智能转换，可以将所生成代码的行为<!--
-->更改为更可预测、更稳定，等等。

启用渐进模式可能需要重写一些代码，但不会太多——所有在渐进模式<!--
-->启用的修复都已经过精心挑选、通过审核并提供迁移辅助工具。
我们希望对于任何积极维护、即将快速更新到最新语言版本的代码库来说，
渐进模式都是一个不错的选择。

## 内联类

>Inline classes are in [Alpha](components-stability.md). They may change incompatibly and require manual migration in the future.
> We appreciate your feedback on it in [YouTrack](https://youtrack.jetbrains.com/issues/KT).
> See details in the [reference](inline-classes.md).
>
{type="warning"}

Kotlin 1.3 引入了一种新的声明方式——`inline class`。内联类可以看作是<!--
-->普通类的受限版，尤其是内联类必须有且只有一个属性：

```kotlin
inline class Name(val s: String)
```

Kotlin 编译器会使用此限制来积极优化内联类的运行时表示，
并使用底层属性的值替换内联类的实例，其中可能会移除构造函数调用、GC 压力，
以及启用其他优化：

```kotlin
inline class Name(val s: String)
//sampleStart
fun main() {
    // 下一行不会调用构造函数，并且
    // 在运行时，“name”只包含字符串 "Kotlin"
    val name = Name("Kotlin")
    println(name.s) 
}
//sampleEnd
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

详见内联类的[参考文档](inline-classes.md)。

## 无符号整型

> Unsigned integers are in [Beta](components-stability.md).
> Their implementation is almost stable, but migration steps may be required in the future.
> We'll do our best to minimize any changes you will have to make.
>
{type="warning"}

Kotlin 1.3 引入了无符号整型类型：

* `kotlin.UByte`: 无符号 8 比特整数，范围是 0 到 255
* `kotlin.UShort`: 无符号 16 比特整数，范围是 0 到 65535
* `kotlin.UInt`: 无符号 32 比特整数，范围是 0 到 2^32 - 1
* `kotlin.ULong`: 无符号 64 比特整数，范围是 0 到 2^64 - 1

无符号类型也支持其对应有符号类型的大多数操作：

```kotlin
fun main() {
//sampleStart
// 可以使用字面值后缀定义无符号类型：
val uint = 42u 
val ulong = 42uL
val ubyte: UByte = 255u

// 通过标准库扩展可以将有符号类型转换为无符号类型，反之亦然：
val int = uint.toInt()
val byte = ubyte.toByte()
val ulong2 = byte.toULong()

// 无符号类型支持类似的操作符：
val x = 20u + 22u
val y = 1u shl 8
val z = "128".toUByte()
val range = 1u..5u
//sampleEnd
println("ubyte: $ubyte, byte: $byte, ulong2: $ulong2")
println("x: $x, y: $y, z: $z, range: $range")
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

详见其[参考文档](basic-types.md#无符号整型)。

## @JvmDefault

>`@JvmDefault` is [Experimental](components-stability.md). It may be dropped or changed at any time.
> Use it only for evaluation purposes. We appreciate your feedback on it in [YouTrack](https://youtrack.jetbrains.com/issues/KT).
>
{type="warning"}

Kotlin 兼容很多 Java 版本，其中包括不支持默认方法的 Java 6 与 Java 7。
为了方便起见，Kotlin 编译器可以变通突破这个限制，不过这个变通方法与 Java 8 引入的 `default` 方法并不兼容。 

这可能会是 Java 互操作性的一个问题，因此 Kotlin 1.3 引入了 `@JvmDefault` 注解。
以此注解标注的方法会生成为 JVM 平台的 `default` 方法：

```kotlin
interface Foo {
    // 会生成为“default”方法
    @JvmDefault
    fun foo(): Int = 42
}
```

> 警告！以 `@JvmDefault` 注解标注的 API 会对二进制兼容性产生严重影响。
在生产中使用 `@JvmDefault` 之前，请务必仔细阅读其[参考文档页](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.jvm/-jvm-default/index.html)。
>
{type="warning"}

## 标准库

### 多平台随机数

在 Kotlin 1.3 之前没有在所有平台生成随机数的统一方式——我们不得不借助平台相关的解决方案，如
JVM 平台的 `java.util.Random`。这个版本通过引入在所有平台都可用的 `kotlin.random.Random` 类来解决这一问题。

```kotlin
import kotlin.random.Random

fun main() {
//sampleStart
    val number = Random.nextInt(42)  // 数字在区间 [0, 上限) 内
    println(number)
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

### isNullOrEmpty 与 orEmpty 扩展

一些类型的 `isNullOrEmpty` 与 `orEmpty` 扩展已经存在于标准库中。如果接收者是 `null` 或空容器，第一个函数返回 `true`；而如果<!--
-->接收者是 `null`，第二个函数回退为空容器实例。
Kotlin 1.3 为集合、映射以及对象数组提供了类似的扩展。

## 在两个现有数组间复制元素

为包括无符号整型数组在内的现有数组类型新增的函数
`array.copyInto(targetArray, targetOffset, startIndex, endIndex)` 使在纯 Kotlin 中实现基于数组的容器更容易。

```kotlin
fun main() {
//sampleStart
    val sourceArr = arrayOf("k", "o", "t", "l", "i", "n")
    val targetArr = sourceArr.copyInto(arrayOfNulls<String>(6), 3, startIndex = 3, endIndex = 6)
    println(targetArr.contentToString())
    
    sourceArr.copyInto(targetArr, startIndex = 0, endIndex = 3)
    println(targetArr.contentToString())
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

### associateWith

一个很常见的情况是，有一个键的列表，希望通过将其中的每个键与某个值相关联来构建映射。
以前可以通过 `associate { it to getValue(it) }` 函数来实现，不过现在我们引入了一种更<!--
-->高效、更易读的替代方式：`keys.associateWith { getValue(it) }`。

```kotlin
fun main() {
//sampleStart
    val keys = 'a'..'f'
    val map = keys.associateWith { it.toString().repeat(5).capitalize() }
    map.forEach { println(it) }
//sampleEnd
}
```

### ifEmpty 与 ifBlank 函数

集合、映射、对象数组、字符序列以及序列现在都有一个 `ifEmpty` 函数，它可以指定一个<!--
-->备用值，当接收者为空容器时以该值代替接收者使用：

```kotlin
fun main() {
//sampleStart
    fun printAllUppercase(data: List<String>) {
        val result = data
        .filter { it.all { c -> c.isUpperCase() } }
            .ifEmpty { listOf("<no uppercase>") }
        result.forEach { println(it) }
    }
    
    printAllUppercase(listOf("foo", "Bar"))
    printAllUppercase(listOf("FOO", "BAR"))
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

此外，字符序列与字符串还有一个 `ifBlank` 扩展，它与 `ifEmpty` 类似，只是会检测<!--
-->字符串是否全部都是空白符而不只是空串。

```kotlin
fun main() {
//sampleStart
    val s = "    \n"
    println(s.ifBlank { "<blank>" })
    println(s.ifBlank { null })
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

### 反射中的密封类

我们在 `kotlin-reflect` 中添加了一个新的 API，可以列出密封（`sealed`）类的所有直接子类型，即 `KClass.sealedSubclasses`。

### 小改动

* `Boolean` 类型现在有伴生对象了。
* `Any?.hashCode()` 扩展函数对 `null` 返回 0。
* `Char` 现在提供了 `MIN_VALUE` 与 `MAX_VALUE` 常量。
* 原生类型伴生对象中的常量 `SIZE_BYTES` 与  `SIZE_BITS`。

## 工具

### IDE 中的代码风格支持

Kotlin 1.3 在 IntelliJ IDEA 中引入了对[推荐代码风格](coding-conventions.md)的支持。
其迁移指南参见[这里](code-style-migration-guide.md)。

### kotlinx.serialization

[kotlinx.serialization](https://github.com/Kotlin/kotlinx.serialization) 是一个在 Kotlin 中为
（反）序列化对象提供多平台支持的库。以前，它是一个独立项目，不过自 Kotlin 1.3 起，
它与其他编译器插件一样随 Kotlin 编译器发行版一起发行。其主要区别在于，无需人为关注<!--
-->序列化 IDE 插件与正在使用的 Kotlin IDE 插件是否兼容了：现在 Kotlin IDE 插件<!--
-->已经包含了序列化支持！

详见[这里](https://github.com/Kotlin/kotlinx.serialization#current-project-status)。

> 尽管 kotlinx.serialization 现在随 Kotlin 编译器发行版一起发行，但  in Kotlin 1.3 它仍是一个**实验性的**特性。 
>
{type="warning"}

### 脚本更新

>Scripting is [Experimental](components-stability.md). It may be dropped or changed at any time.
> Use it only for evaluation purposes. We appreciate your feedback on it in [YouTrack](https://youtrack.jetbrains.com/issues/KT).
>
{type="warning"}

Kotlin 1.3 继续发展与改进脚本 API，为脚本定制引入了一些实验性支持，
例如添加外部属性、提供静态或动态依赖等等。 

关于其他细节，请参考 [KEEP-75](https://github.com/Kotlin/KEEP/blob/master/proposals/scripting-support.md)。

### 草稿文件支持

Kotlin 1.3 引入了对可运行的*草稿文件（scratch files）*的支持。*草稿文件*是一个扩展名为 .kts 的 kotlin 脚本文件，
可以在编辑器中直接运行并获取求值结果。

更多细节请参考通用[草稿文件文档](https://www.jetbrains.com/help/idea/scratches.html)。

