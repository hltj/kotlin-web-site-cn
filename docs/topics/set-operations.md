[//]: # (title: Set 相关操作)

# Set Specific Operations

Kotlin 集合包中包含 set 常用操作的扩展函数：查找交集、并集或差集。

要将两个集合合并为一个（并集），可使用 [`union()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/union.html) 函数。也能以中缀形式使用 `a union b`。
注意，对于有序集合，操作数的顺序很重要：在结果集合中，左侧操作数在前。

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


注意， `List` 也支持 Set 操作。
但是，对 List 进行 Set 操作的结果仍然是 `Set` ，因此将删除所有重复的元素。
