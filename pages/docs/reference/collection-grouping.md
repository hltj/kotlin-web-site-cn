---
type: doc
layout: reference
category: "Collections"
title: "分组"
---

# 分组

Kotlin 标准库提供了用于对集合元素进行分组的扩展函数。
基本函数 [`groupBy()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/group-by.html) 接收一个 lambda 表达式并且返回一个 `Map`。
在这个 map 中，每个键是 lambda 表达式的结果并且相应的值是返回此结果的元素的 `List`。
例如，可以使用这个函数将一个 `String` 列表按首字母分组。

你也可以使用第二个 lambda 表达式参数——一个值转换函数来调用 `groupBy()`。
在具有两个 lambda 表达式的 `groupBy()` 的结果 map 中，由 `keySelector` 函数生成的键被映射到值转换函数的结果，而不是原始元素。

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

```kotlin
fun main() {
//sampleStart
    val numbers = listOf("one", "two", "three", "four", "five")

    println(numbers.groupBy { it.first().toUpperCase() })
    println(numbers.groupBy(keySelector = { it.first() }, valueTransform = { it.toUpperCase() }))
//sampleEnd
}
```
</div>

如果要对元素进行分组，然后一次性地将一个操作应用于所有分组，那么请使用 [`groupingBy()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/grouping-by.html) 函数。
它返回一个 [`Grouping`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-grouping/index.html) 类型的实例。
`Grouping` 实例让你能够以一种惰性的方式将操作应用于所有分组：这些分组实际上是在操作执行之前构建的。

也就是说，`Grouping` 支持以下操作：

* [`eachCount()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/each-count.html) 计算每个分组中元素的数量。
* [`fold()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/fold.html) 和 [`reduce()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/reduce.html) 将每个分组作为一个单独的集合来执行 [fold 和 reduce 操作](collection-aggregate.html#fold-与-reduce)并返回结果。
* [`aggregate()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/aggregate.html) 随后将给定的操作应用于每个分组中的所有元素并返回结果。
   这是对一个 `Grouping` 执行任何操作的通用方法。当 fold 或 reduce 不够用时，可以使用它来实现自定义操作。

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

```kotlin
fun main() {
//sampleStart
    val numbers = listOf("one", "two", "three", "four", "five", "six")
    println(numbers.groupingBy { it.first() }.eachCount())
//sampleEnd
}
```
</div>

