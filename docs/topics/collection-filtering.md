[//]: # (title: 过滤集合)

过滤是最常用的集合处理任务之一。<!--
-->在Kotlin中，过滤条件由 _谓词_ 定义——接受一个集合元素并且返回布尔值的 lambda 表达式，
其返回值含义：`true` 说明给定元素与谓词匹配，`false` 则表示不匹配。

标准库包含了一组让你能够通过单个调用就可以过滤集合的扩展函数。
这些函数不会改变原始集合，因此它们[既可用于可变集合也可用于只读集合](collections-overview.md#集合类型)。
为了操作过滤结果，应该在过滤后将其赋值给变量或链接其他函数。

## 按谓词过滤

基本的过滤函数是 [`filter()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter.html)。<!--
-->当使用一个谓词来调用时，`filter()` 返回与其匹配的集合元素。<!--
-->对于 `List` 和` Set`，过滤结果都是一个 `List`，对 `Map` 来说结果还是一个 `Map`。

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
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

`filter()` 中的谓词只能检查元素的值。<!--
-->如果想在过滤中使用元素在集合中的位置，应该使用 [`filterIndexed()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter-indexed.html)。<!--
-->它接受一个带有两个参数的谓词：元素的索引和元素的值。

如果想使用否定条件来过滤集合，请使用 [`filterNot()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter-not.html)。<!--
-->它返回一个让谓词产生 `false` 的元素列表。

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
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

还有一些函数能够通过过滤给定类型的元素来缩小元素的类型：

* [`filterIsInstance()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter-is-instance.html)
  返回给定类型的集合元素。在一个 `List<Any>` 上被调用时，`filterIsInstance<T>()` 返回一个 `List<T>`，
  从而让你能够在集合元素上调用 `T` 类型的函数。

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
    {kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

* [`filterNotNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter-not-null.html)
  返回所有的非空元素。在一个 `List<T?>` 上被调用时，`filterNotNull()` 返回一个 `List<T: Any>`，从而让你能够将所有元素视为<!--
  -->非空对象。

    ```kotlin
    fun main() {
    //sampleStart
        val numbers = listOf(null, "one", "two", null)
        numbers.filterNotNull().forEach {
        println(it.length)   // 对可空的 String 来说长度不可用
        }
    //sampleEnd
    }
    ```
    {kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

## 划分

另一个过滤函数 – [`partition()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/partition.html)
– 通过一个谓词过滤集合并且将不匹配的元素存放在一个单独的列表中。<!--
-->因此，你得到一个 `List` 的 `Pair` 作为返回值：第一个列表包含与谓词匹配的元素并且<!--
-->第二个列表包含原始集合中的所有其他元素。

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
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

## 检验谓词

最后，有些函数只是针对集合元素简单地检测一个谓词：

* 如果至少有一个元素匹配给定谓词，那么 [`any()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/any.html) 返回 `true`。
* 如果没有元素与给定谓词匹配，那么 [`none()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/none.html) 返回 `true`。
* 如果所有元素都匹配给定谓词，那么 [`all()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/all.html) 返回 `true`。
  请注意，在一个空集合上使用任何有效的谓词去调用 `all()` 都会返回 `true` 。这种行为在逻辑上被称为 [_vacuous truth_](https://en.wikipedia.org/wiki/Vacuous_truth)。

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
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

`any()` 和 `none()` 也可以不带谓词使用：在这种情况下它们只是用来检查集合是否为空。
如果集合中有元素，`any()` 返回 `true`，否则返回 `false`；`none()` 则相反。

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
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}
