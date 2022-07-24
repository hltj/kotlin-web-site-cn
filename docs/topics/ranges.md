[//]: # (title: 区间与数列)

Kotlin 可通过调用 `kotlin.ranges` 包中的 [`rangeTo()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.ranges/range-to.html)
函数及其操作符形式的 `..` 轻松地创建两个值的区间。 通常，`rangeTo()` 会辅以 `in` 或
`!in` 函数。

```kotlin
fun main() {
    val i = 1 
//sampleStart
    if (i in 1..4) { // 等同于 i >= 1 && i <= 4
        print(i)
    }
//sampleEnd
}
```
{kotlin-runnable="true"}

整数类型区间（[`IntRange`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.ranges/-int-range/index.html)、
[`LongRange`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.ranges/-long-range/index.html)、
[`CharRange`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.ranges/-char-range/index.html)）还有一个拓展特性：
可以对其进行迭代。 这些区间也是相应整数类型的[等差数列](https://zh.wikipedia.org/wiki/%E7%AD%89%E5%B7%AE%E6%95%B0%E5%88%97)<!--
-->。

这种区间通常用于 `for` 循环中的迭代。

```kotlin

fun main() {
//sampleStart
    for (i in 1..4) print(i)
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

如需反向迭代数字，请使用 [`downTo`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.ranges/down-to.html)
函数取代 `..` 。

```kotlin

fun main() {
//sampleStart
    for (i in 4 downTo 1) print(i)
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

也可以通过任意步长（不一定为 1 ）迭代数字。 这是通过
[`step`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.ranges/step.html) 函数完成的。

```kotlin

fun main() {
//sampleStart
    for (i in 1..8 step 2) print(i)
    println()
    for (i in 8 downTo 1 step 2) print(i)
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

要迭代不包含其结束元素的数字区间，请使用
[`until`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.ranges/until.html) 函数：

```kotlin

fun main() {
//sampleStart
    for (i in 1 until 10) {       // i in 1 到 10, 不含 10
        print(i)
    }
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

## 区间

区间从数学意义上定义了一个封闭的间隔：它由两个端点值定义，这两个端点值都包含<!--
-->在该区间内。 区间是为可比较类型定义的：具有顺序，可以定义任意<!--
-->实例是否在两个给定实例之间的区间内。

区间的主要操作是 `contains`，通常以 `in` 与 `!in` 操作符的形式使用。
 
要为类创建一个区间，请在区间起始值上调用 `rangeTo()` 函数，并提供结束值作为参数。
`rangeTo()` 通常以操作符 `..` 形式调用。

```kotlin

class Version(val major: Int, val minor: Int): Comparable<Version> {
    override fun compareTo(other: Version): Int {
        if (this.major != other.major) {
            return this.major - other.major
        }
        return this.minor - other.minor
    }
}

fun main() {
//sampleStart
    val versionRange = Version(1, 11)..Version(1, 30)
    println(Version(0, 9) in versionRange)
    println(Version(1, 20) in versionRange)
//sampleEnd
}

```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

## 数列

如上个示例所示，整数类型的区间（例如 `Int`、`Long` 与 `Char`）可视为<!--
-->[等差数列](https://zh.wikipedia.org/wiki/%E7%AD%89%E5%B7%AE%E6%95%B0%E5%88%97)。
在 Kotlin 中，这些数列由特殊类型定义：[`IntProgression`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.ranges/-int-progression/index.html)、
[`LongProgression`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.ranges/-long-progression/index.html)
与 [`CharProgression`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.ranges/-char-progression/index.html)。

数列具有三个基本属性：`first` 元素、`last` 元素和一个非零的 `step`。
首个元素为 `first`，后续元素是前一个元素加上一个 `step`。
以确定的步长在数列上进行迭代等效于 Java/JavaScript 中基于索引的 `for` 循环。

```java
for (int i = first; i <= last; i += step) {
  // ……
}
```

通过迭代数列隐式创建区间时，此数列的 `first` 与 `last` 元素是<!--
-->区间的端点，`step` 为 1 。

```kotlin

fun main() {
//sampleStart
    for (i in 1..10) print(i)
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

要指定数列步长，请在区间上使用 `step` 函数。

```kotlin

fun main() {
//sampleStart
    for (i in 1..8 step 2) print(i)
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

数列的 `last` 元素是这样计算的：
* 对于正步长：不大于结束值且满足 `(last - first) % step == 0` 的最大值。
* 对于负步长：不小于结束值且满足 `(last - first) % step == 0` 的最小值。

因此，`last` 元素并非总与指定的结束值相同。

```kotlin

fun main() {
//sampleStart
    for (i in 1..9 step 3) print(i) // 最后一个元素是 7
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

要创建反向迭代的数列，请在定义其区间时使用 `downTo` 而不是 `..`。

```kotlin

fun main() {
//sampleStart
    for (i in 4 downTo 1) print(i)
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

If you already have a progression, you can iterate it in reverse order with the [`reversed`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.ranges/reversed.html) function:

```kotlin
fun main() {
//sampleStart
    for (i in (1..4).reversed()) print(i)
//sampleEnd
}
```
{kotlin-runnable="true"}

数列实现 `Iterable<N>`，其中 `N` 分别是 `Int`、`Long` 或 `Char`，因此可以在各种<!--
-->[集合函数](collection-operations.md)（如 `map`、`filter` 与其他）中使用它们。

```kotlin

fun main() {
//sampleStart
    println((1..10).filter { it % 2 == 0 })
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

