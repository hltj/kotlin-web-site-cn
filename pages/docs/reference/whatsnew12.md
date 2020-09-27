---
type: doc
layout: reference
title: "Kotlin 1.2 的新特性"
---

# Kotlin 1.2 的新特性

## 目录

* [多平台项目](#多平台项目实验性的)
* [其他语言特性](#其他语言特性)
* [标准库](#标准库)
* [JVM 后端](#jvm-后端)
* [JavaScript 后端](#javascript-后端)

{:#多平台项目实验性的}

## 多平台项目（实验性的）

多平台项目是 Kotlin 1.2 中的一个新的**实验性的**特性，允许你在支持 Kotlin 的目标平台——JVM、JavaScript 以及（将来的）Native 之间重用代码。在多平台项目中，你有三种模块：

* 一个*公共*模块包含平台无关代码，以及无实现的依赖平台的 API 声明。
* *平台*模块包含通用模块中的平台相关声明在指定平台的实现，以及其他平台相关代码。
* 常规模块面向指定的平台，既可以是平台模块的依赖，也可以依赖平台模块。

当你为指定平台编译多平台项目时，既会生成公共代码也会生成平台相关代码。

多平台项目支持的一个主要特点是可以通过**预期声明与实际声明**来表达公共代码对平台相关部分的依赖关系。一个预期声明指定一个 API（类、接口、注解、顶层声明等）。一个实际声明要么是该 API 的平台相关实现，要么是一个引用到在一个外部库中该 API 的一个既有实现的别名。这是一个示例：

在公共代码中：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
// 预期平台相关 API:
expect fun hello(world: String): String

fun greet() {
    // 该预期 API 的用法：
    val greeting = hello("multi-platform world")
    println(greeting)
}

expect class URL(spec: String) {
    open fun getHost(): String
    open fun getPath(): String
}
```

</div>

在 JVM 平台代码中：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
actual fun hello(world: String): String =
    "Hello, $world, on the JVM platform!"

// 使用既有平台相关实现：
actual typealias URL = java.net.URL
```

</div>

关于构建多平台项目的详细信息与步骤，请参见其[文档](http://www.kotlincn.net/docs/reference/multiplatform.html)<!--
-->。

## 其他语言特性

### 注解中的数组字面值

自 Kotlin 1.2 起，注解的数组参数可以通过新的数组字面值语法传入，而无需<!--
-->使用 `arrayOf` 函数：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
@CacheConfig(cacheNames = ["books", "default"])
public class BookRepositoryImpl {
    // ……
}
```

</div>

该数组字面值语法仅限于注解参数。

### lateinit 顶层属性与局部变量

`lateinit` 修饰符现在可以用于顶层属性与局部变量了。例如，后者可用于<!--
-->当一个 lambda 表达式作为构造函数参数传给一个对象时，引用另一个<!--
-->必须稍后定义的对象：

<div class="sample" markdown="1" data-min-compiler-version="1.2" theme="idea">

```kotlin
class Node<T>(val value: T, val next: () -> Node<T>)

fun main(args: Array<String>) {
    // 三个节点的环：
    lateinit var third: Node<Int>

    val second = Node(2, next = { third })
    val first = Node(1, next = { second })

    third = Node(3, next = { first })

    val nodes = generateSequence(first) { it.next() }
    println("Values in the cycle: ${nodes.take(7).joinToString { it.value.toString() }}, ...")
}
```

</div>

### 检测 lateinit 变量是否已初始化

现在可以通过属性引用的 `isInitialized` 来检测该 lateinit var 是否已初始化：

<div class="sample" markdown="1" data-min-compiler-version="1.2" theme="idea">

```kotlin
class Foo {
    lateinit var lateinitVar: String

    fun initializationLogic() {
//sampleStart
        println("isInitialized before assignment: " + this::lateinitVar.isInitialized)
        lateinitVar = "value"
        println("isInitialized after assignment: " + this::lateinitVar.isInitialized)
//sampleEnd
    }
}

fun main(args: Array<String>) {
	Foo().initializationLogic()
}
```

</div>

### 内联函数带有默认函数式参数

内联函数现在允许其内联函式数参数具有默认值：

<div class="sample" markdown="1" data-min-compiler-version="1.2" theme="idea" auto-indent="false">

```kotlin
//sampleStart
inline fun <E> Iterable<E>.strings(transform: (E) -> String = { it.toString() }) =
    map { transform(it) }

val defaultStrings = listOf(1, 2, 3).strings()
val customStrings = listOf(1, 2, 3).strings { "($it)" } 
//sampleEnd

fun main(args: Array<String>) {
    println("defaultStrings = $defaultStrings")
    println("customStrings = $customStrings")
}
```

</div>

### 源自显式类型转换的信息会用于类型推断

 Kotlin 编译器现在可将类型转换信息用于类型推断。如果你调用一个<!--
-->返回类型参数 `T` 的泛型方法并将返回值转换为指定类型 `Foo`，那么编译器现在知道<!--
-->对于本次调用需要绑定类型为 `Foo`。

这对于 Android 开发者来说尤为重要，因为编译器现在可以正确分析
 Android API 级别 26中的泛型 `findViewById` 调用：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
val button = findViewById(R.id.button) as Button
```

</div>

### 智能类型转换改进

当一个变量有安全调用表达式与空检测赋值时，其智能转换现在也可以应用于<!--
-->安全调用接收者：

<div class="sample" markdown="1" data-min-compiler-version="1.2" theme="idea" auto-indent="false" indent="2">

```kotlin
fun countFirst(s: Any): Int {
//sampleStart
    val firstChar = (s as? CharSequence)?.firstOrNull()
    if (firstChar != null)
    return s.count { it == firstChar } // s: Any 会智能转换为 CharSequence

    val firstItem = (s as? Iterable<*>)?.firstOrNull()
    if (firstItem != null)
    return s.count { it == firstItem } // s: Any 会智能转换为 Iterable<*>
//sampleEnd
    return -1
}

fun main(args: Array<String>) {
  val string = "abacaba"
  val countInString = countFirst(string)
  println("called on \"$string\": $countInString")

  val list = listOf(1, 2, 3, 1, 2)
  val countInList = countFirst(list)
  println("called on $list: $countInList")
}
```

</div>

智能转换现在也允许用于在 lambda 表达式中局部变量，只要这些局部变量仅在 lambda 表达式之前修改即可：

<div class="sample" markdown="1" data-min-compiler-version="1.2" theme="idea">

```kotlin
fun main(args: Array<String>) {
//sampleStart
    val flag = args.size == 0
    var x: String? = null
    if (flag) x = "Yahoo!"

    run {
        if (x != null) {
            println(x.length) // x 会智能转换为 String
        }
    }
//sampleEnd
}
```

</div>

### 支持 ::foo 作为 this::foo 的简写

现在写绑定到 `this` 成员的可调用引用可以无需显式接收者，即 `::foo` 取代
 `this::foo`。这也使在引用外部接收者的成员的 lambda 表达式中使用可调用引用更加方便<!--
-->。

### 破坏性变更：try 块后可靠智能转换

 Kotlin 以前将 `try` 块中的赋值语句用于块后的智能转换，这可能会破坏类型安全与空安全<!--
-->并引发运行时故障。这个版本修复了该问题，使智能转换更加严格，但可能会破坏一些<!--
-->依靠这种智能转换的代码。

如果要切换到旧版智能转换行为，请传入回退标志 `-Xlegacy-smart-cast-after-try` 作为编译器<!--
-->参数。该参数会在 Kotlin 1.3中弃用。

### 弃用：数据类弃用 copy

当从已具有签名相同的 `copy` 函数的类型派生数据类时，为数据类生成的 `copy`
实现使用超类型的默认值，这导致反直觉行为，
或者导致运行时失败，如果超类型中没有默认参数的话。

导致 `copy` 冲突的继承在 Kotlin 1.2 中已弃用并带有警告，
而在 Kotlin 1.3中将会是错误。

### 弃用：枚举条目中的嵌套类型

由于初始化逻辑的问题，已弃用在枚举条目内部定义一个非 `inner class`
的嵌套类。这在 Kotlin 1.2 中会引起警告，而在 Kotlin 1.3中会成为错误。

### 弃用：vararg 单个具名参数

为了与注解中的数组字面值保持一致，向一个具名参数形式的 vararg 参数传入单个项目<!--
-->的用法（`foo(items = i)`）已被弃用。请使用伸展操作符连同相应的<!--
-->数组工厂函数：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
foo(items = *intArrayOf(1))
```

</div>

在这种情况下有一项防止性能下降的优化可以消除冗余的数组创建。
单参数形式在 Kotlin 1.2 中会产生警告，而在 Kotlin 1.3中会放弃。

### 弃用：扩展 Throwable 的泛型类的内部类

继承自 `Throwable` 的泛型类的内部类可能会在 throw-catch 场景中违反类型安全性，因此<!--
-->已弃用，在 Kotlin 1.2 中会是警告，而在 Kotlin 1.3中会是错误。

### 弃用：修改只读属性的幕后字段

通过在自定义 getter 中赋值 `field = ……` 来修改只读属性的幕后字段的用法已被弃用，
在 Kotlin 1.2 中会是警告，而在 Kotlin 1.3中会是错误。

## 标准库

### Kotlin 标准库构件与拆分包

 Kotlin 标准库现在完全兼容 Java 9 的模块系统，它禁止拆分包
（多个 jar 文件声明的类在同一包中）。为了支持这点，我们引入了新的 `kotlin-stdlib-jdk7`
与 `kotlin-stdlib-jdk8` ，它们取代了旧版的 `kotlin-stdlib-jre7` 与 `kotlin-stdlib-jre8`。

在 Kotlin 看来新的构件中的声明在相同的包名内，而在
 Java看来有不同的包名。因此，切换到新的构件无需修改任何<!--
-->源代码。

确保与新的模块系统兼容的另一处变更是在 `kotlin-reflect` 库中删除了
`kotlin.reflect` 包中弃用的声明。如果你正在使用它们，你需要<!--
-->切换到使用 `kotlin.reflect.full` 包中的声明，自 Kotlin 1.1 起就支持这个包了。

### windowed、chunked、zipWithNext

用于 `Iterable<T>`、 `Sequence<T>`与 `CharSequence` 的新的扩展覆盖了这些应用场景：缓存或<!--
-->批处理（`chunked`）、 滑动窗口与计算滑动均值（`windowed`）以及处理成对<!--
-->的后续条目（`zipWithNext`）：

<div class="sample" markdown="1" data-min-compiler-version="1.2" theme="idea">

```kotlin
fun main(args: Array<String>) {
//sampleStart
    val items = (1..9).map { it * it }

    val chunkedIntoLists = items.chunked(4)
    val points3d = items.chunked(3) { (x, y, z) -> Triple(x, y, z) }
    val windowed = items.windowed(4)
    val slidingAverage = items.windowed(4) { it.average() }
    val pairwiseDifferences = items.zipWithNext { a, b -> b - a }
//sampleEnd

    println("items: $items\n")

    println("chunked into lists: $chunkedIntoLists")
    println("3D points: $points3d")
    println("windowed by 4: $windowed")
    println("sliding average by 4: $slidingAverage")
    println("pairwise differences: $pairwiseDifferences")
}
```

</div>

### fill、replaceAll、shuffle/shuffled

添加了一些用于操作列表的扩展函数：`MutableList` 的 `fill`、 `replaceAll` 与 `shuffle` ，
以及只读 `List`的 `shuffled`：

<div class="sample" markdown="1" data-min-compiler-version="1.2" theme="idea">

```kotlin
fun main(args: Array<String>) {
//sampleStart
    val items = (1..5).toMutableList()
    
    items.shuffle()
    println("Shuffled items: $items")
    
    items.replaceAll { it * 2 }
    println("Items doubled: $items")
    
    items.fill(5)
    println("Items filled with 5: $items")
//sampleEnd
}
```

</div>

### kotlin-stdlib 中的数学运算

为满足由来已久的需求，Kotlin 1.2 添加了 JVM 与 JS 公用的用于数学运算的 `kotlin.math` API，
包含以下内容：

* 常量：`PI` 与 `E`；
* 三角函数：`cos`、 `sin`、 `tan` 及其反函数：`acos`、 `asin`、 `atan`、 `atan2`；
* 双曲函数：`cosh`、 `sinh`、 `tanh` 及其反函数：`acosh`、 `asinh`、 `atanh`
* 指数函数：`pow`（扩展函数）、 `sqrt`、 `hypot`、 `exp`、 `expm1`；
* 对数函数：`log`、 `log2`、 `log10`、 `ln`、 `ln1p`；
* 取整:
    * `ceil`、 `floor`、 `truncate`、 `round`（奇进偶舍）函数；
    * `roundToInt`、 `roundToLong`（四舍五入）扩展函数；
* 符号与绝对值：
    * `abs` 与 `sign` 函数；
    * `absoluteValue` 与 `sign` 扩展属性；
    * `withSign` 扩展函数；
* 两个数的最值函数：`max` 与 `min`；
* 二进制表示：
    * `ulp` 扩展属性；
    * `nextUp`、 `nextDown`、 `nextTowards` 扩展函数；
    * `toBits`、 `toRawBits`、 `Double.fromBits`（这些在 `kotlin` 包中）。

这些函数同样也有 `Float` 参数版本（但不包括常量）。

### 用于 BigInteger 与 BigDecimal 的操作符与转换

Kotlin 1.2 引入了一些使用  `BigInteger` 与 `BigDecimal` 运算以及由其他<!--
-->数字类型创建它们的函数。具体如下：

* `toBigInteger` 用于 `Int` 与 `Long`；
* `toBigDecimal` 用于 `Int`、 `Long`、 `Float`、 `Double` 以及 `BigInteger`；
* 算术与位运算操作符函数：
    * 二元操作符  `+`、 `-`、 `*`、 `/`、 `%` 以及中缀函数 `and`、 `or`、 `xor`、 `shl`、 `shr`；
    * 一元操作符 `-`、 `++`、 `--` 以及函数 `inv`。

### 浮点数到比特的转换

添加了用于将 `Double` 及 `Float` 与其比特表示形式相互转换的函数：

* `toBits` 与 `toRawBits` 对于 `Double` 返回 `Long` 而对于 `Float` 返回 `Int`；
* `Double.fromBits` 与 `Float.fromBits` 用于有相应比特表示形式创建浮点数。

### 正则表达式现在可序列化

`kotlin.text.Regex` 类现在已经是 `Serializable` 的了并且可用在可序列化的继承结构中。

### 如果可用，Closeable.use 会调用 Throwable.addSuppressed

当在其他异常之后关闭资源期间抛出一个异常，`Closeable.use` 函数会调用 `Throwable.addSuppressed`
。

要启用这个行为，需要依赖项中有 `kotlin-stdlib-jdk7`。

## JVM 后端

### 构造函数调用规范化

自 1.0 版起，Kotlin 就已支持带有复杂控制流的表达式，诸如 try-catch 表达式以及<!--
-->内联函数。根据 Java 虚拟机规范这样的代码是有效的。不幸的是，
当这样的表达式出现在构造函数调用的参数中时，一些字节码处理工具不能很好地处理这种代码
。

为了缓解这种字节码处理工具用户的这一问题，我们添加了一个命令行<!--
-->选项（`-Xnormalize-constructor-calls=模式`），告诉编译器为这样的构造过程生成更接近 Java 的字节码
。其中`模式`是下列之一：

* `disable`（默认）——以与 Kotlin 1.0 即 1.1 相同的方式生成字节码；
* `enable`——为构造函数调用生成类似 Java 的字节码。 这可能会改变类加载与初始化的顺序
；
* `preserve-class-initialization`——为构造函数调用生成类似 Java 的字节码，并确保类<!--
-->初始化顺序得到保留。这可能会影响应用程序的整体性能；仅用在<!--
-->多个类之间共享一些复杂状态并在类初始化时更新的场景中。

“人工”解决办法是将具有控制流的子表达式的值存储在变量中，而不是<!--
-->直接在调用参数内对其求值。这与 `-Xnormalize-constructor-calls=enable` 类似。

### Java 默认方法调用

在 Kotlin 1.2 之前，面向 JVM 1.6 的接口成员覆盖 Java 默认方法会产生一个关于<!--
-->超类型调用的警告：`Super calls to Java default methods are deprecated in JVM target 1.6. Recompile with '-jvm-target 1.8'`（“面向 JVM 1.6 的 Java 默认方法的超类型调用已弃用，请使用‘-jvm-target 1.8’重新编译”）。
在 Kotlin 1.2 中，这是一个**错误**，因此这样的代码都需要面向 JVM 1.8 编译。

### 破坏性变更：平台类型 x.equals(null) 的一致行为

在映射到 Java 原生类型
（`Int!`、 `Boolean!`、 `Short!`、 `Long!`、 `Float!`、 `Double!`、 `Char!`）的平台类型上调用 `x.equals(null)`，当 `x` 为 `null` 时错误地返回了 `true`。
自 Kotlin 1.2 起，在平台类型的空值上调用 `x.equals(……)` 都会**抛出 NPE**
（但 `x == ...` 不会）。

要返回到 1.2 之前的行为，请将标志 `-Xno-exception-on-explicit-equals-for-boxed-null` 传给编译器。

### 破坏性变更：修正平台 null 透过内联扩展接收者逃逸

在平台类型的空值上调用内联扩展函数并没有检测接收者是否为 null，
因而允许 null 逃逸到其他代码中。Kotlin 1.2 在调用处强制执行这项检测，
如果接收者为空就抛出异常。

要切换到旧版行为，请将回退标志 `-Xno-receiver-assertions` 传给编译器。

## JavaScript 后端

### 默认启用 TypedArray 支持

将 Kotlin 原生数组（如 `IntArray`、 `DoubleArray` 等）
翻译为 [JavaScript 有类型数组](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Typed_arrays)的 JS 有类型数组支持<!--
-->之前是选择性加入的功能，现在已默认启用。

## 工具

### 警告作为错误

编译器现在提供一个将所有警告视为错误的选项。可在命令行中使用 `-Werror`，或者<!--
-->在 Gradle 中使用以下代码片段：

<div class="sample" markdown="1" mode="groovy" theme="idea">

```groovy
compileKotlin {
    kotlinOptions.allWarningsAsErrors = true
}
```

</div>
