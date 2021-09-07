[//]: # (title: 分组)

Kotlin 标准库提供用于对集合元素进行分组的扩展函数。
基本函数 [`groupBy()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/group-by.html) 使用一个 lambda 函数并返回一个 `Map`。
在此 Map 中，每个键都是 lambda 结果，而对应的值是返回此结果的元素 `List`。
例如，可以使用此函数将 `String` 列表按首字母分组。

还可以使用第二个 lambda 参数（值转换函数）调用 `groupBy()`。
在带有两个 lambda 的 `groupBy()` 结果 Map 中，由 `keySelector` 函数生成的键映射到值转换函数的结果，而不是原始元素。



```kotlin
fun main() {
//sampleStart
    val numbers = listOf("one", "two", "three", "four", "five")

    println(numbers.groupBy { it.first().toUpperCase() })
    println(numbers.groupBy(keySelector = { it.first() }, valueTransform = { it.toUpperCase() }))
//sampleEnd
}
```


如果要对元素进行分组，然后一次将操作应用于所有分组，请使用 [`groupingBy()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/grouping-by.html) 函数。
它返回一个 [`Grouping`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-grouping/index.html) 类型的实例。
通过 `Grouping` 实例，可以以一种惰性的方式将操作应用于所有组：这些分组实际上是刚好在执行操作前构建的。

即，`Grouping` 支持以下操作：

* [`eachCount()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/each-count.html) 计算每个组中的元素。
* [`fold()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/fold.html) 与 [`reduce()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/reduce.html) 对每个组分别执行 [fold 与 reduce](collection-aggregate.md#fold-与-reduce) 操作，作为一个单独的集合并返回结果。
* [`aggregate()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/aggregate.html) 随后将给定操作应用于每个组中的所有元素并返回结果。
  这是对 `Grouping` 执行任何操作的通用方法。当折叠或缩小不够时，可使用它来实现自定义操作。



```kotlin
fun main() {
//sampleStart
  val numbers = listOf("one", "two", "three", "four", "five", "six")
  println(numbers.groupingBy { it.first() }.eachCount())
//sampleEnd
}
```


