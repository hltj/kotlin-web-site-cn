[//]: # (title: 加减操作符)

在 Kotlin 中，为集合定义了 [`plus`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/plus.html) (`+`) 和 [`minus`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/minus.html)
(`-`) 操作符。
它们把一个集合作为第一个操作数；第二个操作数可以是一个元素或者是另一个集合。
返回值是一个新的只读集合：

* `plus` 的结果包含原始集合 _和_ 第二个操作数中的元素。
* `minus` 的结果包含原始集合中的元素，但第二个操作数中的元素 _除外_。
  如果第二个操作数是一个元素，那么 `minus` 移除其在原始集合中的 _第一次_ 出现；如果是一个集合，那么移除其元素在原始集合中的 _所有_ 出现。

```kotlin

fun main() {
//sampleStart
    val numbers = listOf("one", "two", "three", "four")

    val plusList = numbers + "five"
    val minusList = numbers - listOf("three", "four")
    println(plusList)
    println(minusList)
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

关于 map 的 `plus` 和 `minus` 操作符的详细信息，请参见 [Map 相关操作](map-operations.md)。
也为集合定义了[广义赋值操作符](operator-overloading.md#assignments) [`plusAssign`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/plus-assign.html)
（`+=`）与 [`minusAssign`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/minus-assign.html) (`-=`)。
然而，对于只读集合，它们实际上使用 `plus` 或者 `minus` 操作符并尝试将结果赋值给同一变量。
因此，它们仅在由 `var` 声明的只读集合中可用。
对于可变集合，如果它是一个 `val`，那么它们会修改集合。更多详细信息请参见[集合写操作](collection-write.md)。
