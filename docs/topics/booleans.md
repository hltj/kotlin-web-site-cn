[//]: # (title: 布尔)

`Boolean` 类型表示可以有 `true` 与 `false` 两个值的布尔对象。
`Boolean` has a [nullable](null-safety.md) counterpart declared as `Boolean?`.

> On the JVM, booleans stored as the primitive `boolean` type typically use 8 bits.
>
{type="note"}

布尔值的内置运算有：

* `||`——析取（逻辑*或*）
* `&&`——合取（逻辑*与*）
* `!`——否定（逻辑*非*）

For example:

```kotlin
fun main() {
//sampleStart
    val myTrue: Boolean = true
    val myFalse: Boolean = false
    val boolNull: Boolean? = null

    println(myTrue || myFalse)
    // true
    println(myTrue && myFalse)
    // false
    println(!myTrue)
    // false
    println(boolNull)
    // null
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

The `||` and `&&` operators work lazily, which means:

* If the first operand is `true`, the `||` operator does not evaluate the second operand.
* If the first operand is `false`, the `&&` operator does not evaluate the second operand.

> 在 JVM 平台，布尔对象的可空引用是装箱的 Java 类，类似于[数字](numbers.md#jvm-平台的数字表示)。
>
{type="note"}
