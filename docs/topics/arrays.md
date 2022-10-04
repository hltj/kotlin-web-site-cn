[//]: # (title: 数组)

数组在 Kotlin 中使用 `Array` 类来表示。它定义了 `get()` 与 `set`() 函数（按照运算符重载约定这会转变为 `[]`）<!--
-->与 `size` 属性及其他有用的成员函数：

```kotlin
class Array<T> private constructor() {
    val size: Int
    operator fun get(index: Int): T
    operator fun set(index: Int, value: T): Unit

    operator fun iterator(): Iterator<T>
    // ...
}
```

可以使用函数 `arrayOf()` 来创建一个数组并传递元素值给它，这样 `arrayOf(1, 2, 3)` 创建了 array `[1, 2, 3]`。
或者，函数 `arrayOfNulls()` 可以用于创建一个指定大小的、所有元素都为空的数组。

另一个选项是用接受数组大小以及一个函数参数的 `Array` 构造函数，用作参数的函数能够返回<!--
-->给定索引的元素：

```kotlin
fun main() {
//sampleStart
    // 创建一个 Array<String> 初始化为 ["0", "1", "4", "9", "16"]
    val asc = Array(5) { i -> (i * i).toString() }
    asc.forEach { println(it) }
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

`[]` 运算符代表调用成员函数 `get()` 与 `set()`。

Kotlin 中数组是*不型变的（invariant）*。这意味着 Kotlin 不让我们把 `Array<String>`
赋值给 `Array<Any>`，以防止可能的运行时失败（但是你可以使用 `Array<out Any>`,
参见[类型投影](generics.md#类型投影)）。

## 原生类型数组

Kotlin 也有无装箱开销的类来表示原生类型数组: `ByteArray`、
`ShortArray`、`IntArray` 等等。这些类与 `Array` 并没有继承关系，但是<!--
-->它们有同样的方法属性集。它们也都有相应的工厂方法:

```kotlin
val x: IntArray = intArrayOf(1, 2, 3)
x[0] = x[1] + x[2]
```

```kotlin
// 大小为 5、值为 [0, 0, 0, 0, 0] 的整型数组
val arr = IntArray(5)

// 用常量初始化数组中的值的示例
// 大小为 5、值为 [42, 42, 42, 42, 42] 的整型数组
val arr = IntArray(5) { 42 }

// 使用 lambda 表达式初始化数组中的值的示例
// 大小为 5、值为 [0, 1, 2, 3, 4] 的整型数组（值初始化为其索引值）
var arr = IntArray(5) { it * 1 } 
```