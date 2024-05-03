[//]: # (title: 相等性)

Kotlin 中有两类相等性：

* *结构*相等（`==`——用 `equals()` 检测）
* *引用*相等（`===`——检测两个引用指向同一对象）

## 结构相等

Structural equality verifies if two objects have the same content or structure. 结构相等由 `==` 操作
及其否定形式 `!=` 操作判断。
按照约定，像 `a == b` 这样的表达式会翻译成：

```kotlin
a?.equals(b) ?: (b === null)
```

如果 `a` 不是 `null` 则调用 `equals(Any?)` 函数。否则（`a` 是 `null`），检测 `b`
是否与`null` 引用相等：

```kotlin
fun main() {
    var a = "hello"
    var b = "hello"
    var c = null
    var d = null
    var e = d

    println(a == b)
    // true
    println(a == c)
    // false
    println(c == e)
    // true
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

请注意，当与 `null` 显式比较时完全没必要优化你的代码：
`a == null` 会被自动转换为 `a === null`。

In Kotlin, the `equals()` function is inherited by all classes from the `Any` class. By default, the `equals()` function 
implements [referential equality](#引用相等). However, classes in Kotlin can override the `equals()` 
function to provide a custom equality logic and, in this way, implement structural equality.

Value classes and data classes are two specific Kotlin types that automatically override the `equals()` function. 
That's why they implement structural equality by default.

However, in the case of data classes, if the `equals()` function is marked as `final` in the parent class, its behavior remains unchanged.

Distinctly, non-data classes (those not declared with the `data` modifier) do not override the 
`equals()` function by default. Instead, non-data classes implement referential equality behavior inherited from the `Any` class.
To implement structural equality, non-data classes require a custom equality logic to override the `equals()` function.

如需提供自定义的相等检测实现，请覆盖
[`equals(other: Any?): Boolean`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-any/equals.html) 函数：

```kotlin
class Point(val x: Int, val y: Int) {
    override fun equals(other: Any?): Boolean {
        if (this === other) return true
        if (other !is Point) return false

        // Compares properties for structural equality
        return this.x == other.x && this.y == other.y
    }
}
```
> When overriding the equals() function, you should also override the [hashCode() function](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-any/hash-code.html) 
> to keep consistency between equality and hashing and ensure a proper behavior of these functions.
>
{type="note"}

名称相同但签名不同的函数（如 `equals(other: Foo)`）并不会影响<!--
-->操作符 `==` 与 `!=` 的相等性检测。

结构相等与 `Comparable<……>` 接口定义的比较无关，因此只有自定义的
`equals(Any?)` 实现可能会影响该操作符的行为。

## 引用相等

Referential equality verifies the memory addresses of two objects to determine if they are the same instance.

引用相等由 `===`（以及其否定形式 `!==`）操作判断。`a === b`
当且仅当 `a` 与 `b` 指向同一个对象时求值为 true： 

```kotlin
fun main() {
    var a = "Hello"
    var b = a
    var c = "world"
    var d = "world"

    println(a === b)
    // true
    println(a === c)
    // false
    println(c === d)
    // true

}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

对于运行时以原生类型表示的值
（例如 `Int`），`===` 相等检测等价于 `==` 检测。

> The referential equality is implemented differently in Kotlin/JS. For more information about equality, see the [Kotlin/JS](js-interop.md#equality) documentation.
>
{type="tip"}

## 浮点数相等性

当相等性检测的两个操作数都是静态已知的（可空或非空的）`Float` 或 `Double` 类型时，该检测遵循
[IEEE 754 浮点数运算标准](https://en.wikipedia.org/wiki/IEEE_754)。

The behavior is different for operands that are not statically typed as floating-point numbers. In these cases,
structural equality is implemented. As a result, checks with operands not statically typed as floating-point numbers differ from the 
IEEE standard. In this scenario:

* `NaN` 等于其自身
* `NaN` 比（包括 `POSITIVE_INFINITY` 在内的）任何其他元素都大
* `-0.0` 不等于 `0.0`

更多信息请参见[浮点数比较](numbers.md#浮点数比较)。

## 数组相等性

如需比较两个数组是否具有相同顺序的相同元素，请使用 [`contentEquals()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/content-equals.html)。

更多信息请参见[比较数组](arrays.md#比较数组)。
