[//]: # (title: 集合操作概述)

Kotlin 标准库提供了用于对集合执行操作的多种函数。这包括简单的操作，例如获取或添加元素，以及更复杂的操作，包括搜索、排序、过滤、转换等。

## 扩展与成员函数

集合操作在标准库中以两种方式声明：集合接口的[成员函数](classes.md#类成员)和[扩展函数](extensions.md#扩展函数)。

成员函数定义了对于集合类型是必不可少的操作。例如，[`Collection`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-collection/index.html) 包含函数 [`isEmpty()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-collection/is-empty.html) 来检查其是否为空； [`List`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list/index.html) 包含用于对元素进行索引访问的 [`get()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list/get.html)，等等。

创建自己的集合接口实现时，必须实现其成员函数。
为了使新实现的创建更加容易，请使用标准库中集合接口的框架实现：[`AbstractCollection`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-abstract-collection/index.html)、[`AbstractList`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-abstract-list/index.html)、[`AbstractSet`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-abstract-set/index.html)、[`AbstractMap`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-abstract-map/index.html) 及其相应可变抽象类。

其他集合操作被声明为扩展函数。这些是过滤、转换、排序和其他集合处理功能。

## 公共操作

公共操作可用于[只读集合与可变集合](collections-overview.md#集合类型)。 常见操作分为以下几类：

* [集合转换](collection-transformations.md)
* [集合过滤](collection-filtering.md)
* [`plus` 与 `minus` 操作符](collection-plus-minus.md)
* [分组](collection-grouping.md)
* [取集合的一部分](collection-parts.md)
* [取单个元素](collection-elements.md)
* [集合排序](collection-ordering.md)
* [集合聚合操作](collection-aggregate.md)

这些页面中描述的操作将返回其结果，而不会影响原始集合。例如，一个过滤操作产生一个*新集合*，其中包含与过滤谓词匹配的所有元素。
此类操作的结果应存储在变量中，或以其他方式使用，例如，传到其他函数中。



```kotlin
fun main() {
//sampleStart
    val numbers = listOf("one", "two", "three", "four")  
    numbers.filter { it.length > 3 }  // `numbers` 没有任何改变，结果丢失
    println("numbers are still $numbers")
    val longerThan3 = numbers.filter { it.length > 3 } // 结果存储在 `longerThan3` 中
    println("numbers longer than 3 chars are $longerThan3")
//sampleEnd
}

```


对于某些集合操作，有一个选项可以指定 _目标_ 对象。
目标是一个可变集合，该函数将其结果项附加到该可变对象中，而不是在新对象中返回它们。
对于执行带有目标的操作，有单独的函数，其名称中带有 `To` 后缀，例如，用 [`filterTo()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter-to.html) 代替 [`filter()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter.html) 以及用 [`associateTo()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/associate-to.html) 代替 [`associate()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/associate.html)。
这些函数将目标集合作为附加参数。



```kotlin
fun main() {
//sampleStart
    val numbers = listOf("one", "two", "three", "four")
    val filterResults = mutableListOf<String>()  // 目标对象
    numbers.filterTo(filterResults) { it.length > 3 }
    numbers.filterIndexedTo(filterResults) { index, _ -> index == 0 }
    println(filterResults) // 包含两个操作的结果
//sampleEnd
}
```


为了方便起见，这些函数将目标集合返回了，因此您可以在函数调用的相应参数中直接创建它：



```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four")
//sampleStart
    // 将数字直接过滤到新的哈希集中，
    // 从而消除结果中的重复项
    val result = numbers.mapTo(HashSet()) { it.length }
    println("distinct item lengths are $result")
//sampleEnd
}
```


具有目标的函数可用于过滤、关联、分组、展平以及其他操作。 有关目标操作的完整列表，请参见 [Kotlin collections reference](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/index.html)。

## 写操作

对于可变集合，还存在可更改集合状态的 _写操作_ 。这些操作包括添加、删除和更新元素。写操作在[集合写操作](collection-write.md)以及 [List 写操作](list-operations.md#list-写操作)与 [Map 写操作](map-operations.md#map-写操作)的相应部分中列出。

对于某些操作，有成对的函数可以执行相同的操作：一个函数就地应用该操作，另一个函数将结果作为单独的集合返回。
例如， [`sort()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sort.html) 就地对可变集合进行排序，因此其状态发生了变化； [`sorted()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sorted.html) 创建一个新集合，该集合包含按排序顺序相同的元素。



```kotlin
fun main() {
//sampleStart
    val numbers = mutableListOf("one", "two", "three", "four")
    val sortedNumbers = numbers.sorted()
    println(numbers == sortedNumbers)  // false
    numbers.sort()
    println(numbers == sortedNumbers)  // true
//sampleEnd
}
```


