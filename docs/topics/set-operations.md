[//]: # (title: Set 相关操作)

Kotlin 集合包中包含 set 常用操作的扩展函数： 找出集合间的<!--
-->交集、并集或差集。

要将两个集合合并为一个（并集），可使用 [`union()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/union.html)
函数。也能以中缀形式使用 `a union b`。
注意，对于有序集合，操作数的顺序很重要：在结果集合中，
左侧操作数在前。

要查找两个集合中都存在的元素（交集），请使用 [`intersect()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/intersect.html) 。
要查找另一个集合中不存在的集合元素（差集），请使用 [`subtract()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/subtract.html) 。
这两个函数也能以中缀形式调用，例如， `a intersect b` 。

```kotlin
fun main() {
//sampleStart
    val numbers = setOf("one", "two", "three")

    println(numbers union setOf("four", "five"))
    println(setOf("four", "five") union numbers)

    println(numbers intersect setOf("two", "one"))
    println(numbers subtract setOf("three", "four"))
    println(numbers subtract setOf("four", "three")) // 相同的输出
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

You can also apply `union`, `intersect`, and `subtract` to `List`.
However, their result is _always_ a `Set`, even on lists. In this result, all the duplicate elements are merged into one 
and the index access is not available.

```kotlin
fun main() {
//sampleStart
    val list1 = listOf(1, 1, 2 ,3, 5, 8, -1)
    val list2 = listOf(1, 1, 2, 2 ,3, 5)
    println(list1 intersect list2) // result on two lists is a Set
    println(list1 union list2)     // equal elements are merged into one
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}
