[//]: # (title: 区间与数列)

Kotlin 可通过调用 `kotlin.ranges` 包中的 [`rangeTo()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.ranges/range-to.html)
及 [`.rangeUntil()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.ranges/range-until.html) 函数轻松地创建两个值的区间
。

To create:
* a closed-ended range, call the `.rangeTo()` function with the `..` operator.
* an open-ended range, call the `.rangeUntil()` function with the `..<` operator.

For example:

```kotlin
fun main() {
//sampleStart
    // Closed-ended range
    println(4 in 1..4)
    // true
    
    // Open-ended range
    println(4 in 1..<4)
    // false
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-ranges-rangeto-rangeuntil"}

Ranges are particularly useful for iterating over `for` loops:

```kotlin
fun main() {
//sampleStart
    for (i in 1..4) print(i)
    // 1234
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-ranges-for-loop"}

如需反向迭代数字，请使用 [`downTo`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.ranges/down-to.html)
函数取代 `..` 。

```kotlin
fun main() {
//sampleStart
    for (i in 4 downTo 1) print(i)
    // 4321
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-ranges-downto"}

也可以通过任意步长（不一定为 1 ）迭代数字。 这是通过
[`step`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.ranges/step.html) 函数完成的。

```kotlin

fun main() {
//sampleStart
    for (i in 0..8 step 2) print(i)
    println()
    // 02468
    for (i in 0..<8 step 2) print(i)
    println()
    // 0246
    for (i in 8 downTo 0 step 2) print(i)
    // 86420
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-ranges-step"}

## 数列

整数类型（例如 `Int`、`Long` 与 `Char`）的区间可视为<!--
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
    // 12345678910
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-ranges-progressions"}

要指定数列步长，请在区间上使用 `step` 函数。

```kotlin

fun main() {
//sampleStart
    for (i in 1..8 step 2) print(i)
    // 1357
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-ranges-progressions-step"}

数列的 `last` 元素是这样计算的：
* 对于正步长：不大于结束值且满足 `(last - first) % step == 0` 的最大值。
* 对于负步长：不小于结束值且满足 `(last - first) % step == 0` 的最小值。

因此，`last` 元素并非总与指定的结束值相同。

```kotlin

fun main() {
//sampleStart
    for (i in 1..9 step 3) print(i) // 最后一个元素是 7
    // 147
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-ranges-progressions-last"}

数列实现 `Iterable<N>`，其中 `N` 分别是 `Int`、`Long` 或 `Char`，因此可以在各种<!--
-->[集合函数](collection-operations.md)（如 `map`、`filter` 与其他）中使用它们。

```kotlin

fun main() {
//sampleStart
    println((1..10).filter { it % 2 == 0 })
    // [2, 4, 6, 8, 10]
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-ranges-progressions-filter"}

