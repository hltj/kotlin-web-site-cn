[//]: # (title: 布尔)

`Boolean` 类型表示可以有 `true` 与 `false` 两个值的布尔对象。

`Boolean` 的可空版 `Boolean?` 还有 `null` 值。

布尔值的内置运算有：

* `||`——析取（逻辑*或*）
* `&&`——合取（逻辑*与*）
* `!`——否定（逻辑*非*）

`||` 与 `&&` 都是惰性（短路）的。

```kotlin
fun main() {
//sampleStart
    val myTrue: Boolean = true
    val myFalse: Boolean = false
    val boolNull: Boolean? = null
    
    println(myTrue || myFalse)
    println(myTrue && myFalse)
    println(!myTrue)
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

> **JVM 平台**： 布尔对象的可空引用会装箱，类似于[数字](numbers.md#jvm-平台的数字表示)。
>
{type="note"}