---
type: doc
layout: reference
category: "Collections"
title: "过滤集合"
---

# 过滤

过滤是集合处理中最流行的任务之一。
在 Kotlin 中，过滤条件的定义是通过 _predicates_ 即以集合元素为参数返回值为 boolean 的 lambda 函数: `true` 表示给定元素断言成功, `false` 表示断言失败.

这个标准库包含一组让你一次调用即可过滤集合的拓展函数。
这些功能不会改变原始集合，所以它们能作用于 [可变的和只读的](collections-overview.html#集合类型) 集合.
过滤后的结果应将其分配给变量或用于链式函数。

## 按谓词过滤

基本的函数是 [`filter()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter.html).
当使用 _predicates_ 作为参数调用时， `filter()` 返回断言正确的集合元素。
对于 `List` 和 `Set`, 结果返回都是 `List`，`Map` 结果则是 `Map`。

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

```kotlin
fun main() {
//sampleStart
    val numbers = listOf("one", "two", "three", "four")  
    val longerThan3 = numbers.filter { it.length > 3 }
    println(longerThan3)

    val numbersMap = mapOf("key1" to 1, "key2" to 2, "key3" to 3, "key11" to 11)
    val filteredMap = numbersMap.filter { (key, value) -> key.endsWith("1") && value > 10}
    println(filteredMap)
//sampleEnd
}
```
</div>

`filter()` 中只能对元素的值进行断言。
如果您想使用元素的下标，使用 [`filterIndexed()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter-indexed.html).
它将使用索引和元素的值两个参数作为断言依据。

想要通过否定条件来过滤，使用 [`filterNot()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter-not.html).
它将返回断言为 `false` 的集合元素。

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

```kotlin
fun main() {
//sampleStart
    val numbers = listOf("one", "two", "three", "four")
    
    val filteredIdx = numbers.filterIndexed { index, s -> (index != 0) && (s.length < 5)  }
    val filteredNot = numbers.filterNot { it.length <= 3 }

    println(filteredIdx)
    println(filteredNot)
//sampleEnd
}
```
</div>

也有给定类型来过滤元素的函数可用于限制元素类型：

* [`filterIsInstance()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter-is-instance.html) 返回给定类型的元素集合。 通过调用 `List<Any>`，`filterIsInstance<T>()` 返回一个 `List<T>`，因此在集合元素中您可以调用 `T` 类型的函数。

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

```kotlin
fun main() {
//sampleStart
    val numbers = listOf(null, 1, "two", 3.0, "four")
    println("All String elements in upper case:")
    numbers.filterIsInstance<String>().forEach {
        println(it.toUpperCase())
    }
//sampleEnd
}
```
</div>
    
* [`filterNotNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter-not-null.html) 返回所有非空元素。 调用 `List<T?>`，`filterNotNull()` 返回一个 `List<T: Any>`，因此您可以将元素视为非空对象。

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

```kotlin
fun main() {
//sampleStart
    val numbers = listOf(null, "one", "two", null)
    numbers.filterNotNull().forEach {
        println(it.length)   // length is unavailable for nullable Strings
    }
//sampleEnd
}
```
</div>

## 划分

Another filtering function – [`partition()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/partition.html) – filters a collection by a predicate and keeps the elements that don't match it in a separate list.
So, you have a `Pair` of `List`s as a return value: the first list containing elements that match the predicate and the second one containing everything else from the original collection.

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

```kotlin
fun main() {
//sampleStart
    val numbers = listOf("one", "two", "three", "four")
    val (match, rest) = numbers.partition { it.length > 3 }

    println(match)
    println(rest)
//sampleEnd
}
```
</div>

## 检验谓词

Finally, there are functions that simply test a predicate against collection elements:

* [`any()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/any.html) returns `true` if at least one element matches the given predicate.
* [`none()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/none.html) returns `true` if none of the elements match the given predicate.
* [`all()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/all.html) returns `true` if all elements match the given predicate. Note that `all()` returns `true` when called with any valid predicate on an empty collection. Such behavior is known in logic as [_vacuous truth_](https://en.wikipedia.org/wiki/Vacuous_truth).

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

```kotlin
fun main() {
//sampleStart
    val numbers = listOf("one", "two", "three", "four")

    println(numbers.any { it.endsWith("e") })
    println(numbers.none { it.endsWith("a") })
    println(numbers.all { it.endsWith("e") })

    println(emptyList<Int>().all { it > 5 })   // vacuous truth
//sampleEnd
}
```
</div>

`any()` and `none()` can also be used without a predicate: in this case they just check the collection emptiness.
`any()` returns `true` if there are elements and `false` if there aren't; `none()` does the opposite.

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

```kotlin
fun main() {
//sampleStart
    val numbers = listOf("one", "two", "three", "four")
    val empty = emptyList<String>()

    println(numbers.any())
    println(empty.any())
    
    println(numbers.none())
    println(empty.none())
//sampleEnd
}
```
</div>

