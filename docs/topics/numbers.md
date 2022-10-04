[//]: # (title: 数字)

## 整数类型

Kotlin 提供了一组表示数字的内置类型。
对于整数，有四种不同大小的类型，因此值的范围也不同：

| 类型	 | 大小（比特数）| 最小值 | 最大值 |
|--------|---------------|--------|------- |
| `Byte`	 | 8             |-128    |127     |
| `Short`	 | 16            |-32768  |32767   |
| `Int`	 | 32            |-2,147,483,648 (-2<sup>31</sup>)| 2,147,483,647 (2<sup>31</sup> - 1)|
| `Long`	 | 64            |-9,223,372,036,854,775,808 (-2<sup>63</sup>)|9,223,372,036,854,775,807 (2<sup>63</sup> - 1)|

When you initialize a variable with no explicit type specification, the compiler automatically infers the type with the 
smallest range enough to represent the value. If it is not exceeding the range of `Int`, the type is `Int`. If it exceeds,
the type is `Long`. To specify the `Long` value explicitly, append the suffix `L` to the value. 
Explicit type specification triggers the compiler to check the value not to exceed the range of the specified type.

```kotlin
val one = 1 // Int
val threeBillion = 3000000000 // Long
val oneLong = 1L // Long
val oneByte: Byte = 1
```

## 浮点类型

对于实数，Kotlin 提供了浮点类型 `Float` 与 `Double` 类型，遵循 [IEEE 754 标准](https://zh.wikipedia.org/wiki/IEEE_754)。
`Float` 表达 IEEE 754 *单精度*，而 `Double` 表达*双精度*。

These types differ in their size and provide storage for floating-point numbers with different precision:

| 类型	 | 大小（比特数）| 有效数字比特数 | 指数比特数 | 十进制位数 |
|--------|---------------|--------------- |------------|------------|
| `Float`	 | 32            |24              |8           |6-7         |
| `Double` | 64            |53              |11          |15-16       |    

可以使用带小数部分的数字初始化 `Double` 与 `Float` 变量。
小数部分与整数部分之间用句点（`.`）分隔
对于以小数初始化的变量，编译器会推断为 `Double` 类型：

```kotlin
val pi = 3.14 // Double
// val one: Double = 1 // 错误：类型不匹配
val oneDouble = 1.0 // Double
```

如需将一个值显式指定为 `Float` 类型，请添加 `f` 或 `F` 后缀。
如果这样的值包含多于 6～7 位十进制数，那么会将其舍入：

```kotlin
val e = 2.7182818284 // Double
val eFloat = 2.7182818284f // Float，实际值为 2.7182817
```

与一些其他语言不同，Kotlin 中的数字没有隐式拓宽转换。
例如，具有 `Double` 参数的函数只能对 `Double` 值调用，而不能对 `Float`、
`Int` 或者其他数字值调用：

```kotlin
fun main() {
    fun printDouble(d: Double) { print(d) }

    val i = 1    
    val d = 1.0
    val f = 1.0f 

    printDouble(d)
//    printDouble(i) // 错误：类型不匹配
//    printDouble(f) // 错误：类型不匹配
}
```

如需将数值转换为不同的类型，请使用[显式转换](#显式数字转换)。

## 数字字面常量

数值常量字面值有以下几种:

* 十进制: `123`
  * Long 类型用大写 `L` 标记: `123L`
* 十六进制: `0x0F`
* 二进制: `0b00001011`

> Kotlin 不支持八进制。
>
{type="note"}

Kotlin 同样支持浮点数的常规表示方法:

* 默认 double：`123.5`、`123.5e10`
* Float 用 `f` 或者 `F` 标记: `123.5f`

你可以使用下划线使数字常量更易读：

```kotlin
val oneMillion = 1_000_000
val creditCardNumber = 1234_5678_9012_3456L
val socialSecurityNumber = 999_99_9999L
val hexBytes = 0xFF_EC_DE_5E
val bytes = 0b11010010_01101001_10010100_10010010
```

> There are also special tags for unsigned integer literals.  
> Read more about [literals for unsigned integer types](unsigned-integer-types.md).
> 
{type="tip"}

## JVM 平台的数字表示

在 JVM 平台数字存储为原生类型 `int`、 `double` 等。 
例外情况是当创建可空数字引用如 `Int?` 或者使用泛型时。
在这些场景中，数字会装箱为 Java 类 `Integer`、 `Double` 等。

对相同数字的可为空引用可能会引用不同的对象：

```kotlin
fun main() {
//sampleStart
    val a: Int = 100
    val boxedA: Int? = a
    val anotherBoxedA: Int? = a
    
    val b: Int = 10000
    val boxedB: Int? = b
    val anotherBoxedB: Int? = b
    
    println(boxedA === anotherBoxedA) // true
    println(boxedB === anotherBoxedB) // false
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

由于 JVM 对 `-128` 到 `127` 的整数（`Integer`）应用了内存优化，因此，`a`
的所有可空引用实际上都是同一对象。但是没有对 `b` 应用内存优化，所以它们是不同对象。

另一方面，它们仍然相等:

```kotlin
fun main() {
//sampleStart
    val b: Int = 10000
    println(b == b) // 输出“true”
    val boxedB: Int? = b
    val anotherBoxedB: Int? = b
    println(boxedB == anotherBoxedB) // 输出“true”
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

## 显式数字转换

由于不同的表示方式，较小类型并*不是*较大类型的子类型。
如果它们是的话，就会出现下述问题：

```kotlin
// 假想的代码，实际上并不能编译：
val a: Int? = 1 // 一个装箱的 Int (java.lang.Integer)
val b: Long? = a // 隐式转换产生一个装箱的 Long (java.lang.Long)
print(b == a) // 惊！这将输出“false”鉴于 Long 的 equals() 会检测另一个是否也为 Long
```

所以会悄无声息地失去相等性，更别说同一性了。

因此较小的类型*__不能__ 隐式转换*为较大的类型。
这意味着把 `Byte` 型值赋给一个 `Int` 变量必须显式转换：

```kotlin
fun main() {
//sampleStart
    val b: Byte = 1 // OK, 字面值会静态检测
    // val i: Int = b // 错误
    val i1: Int = b.toInt()
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

所有数字类型都支持转换为其他类型：

* `toByte(): Byte`
* `toShort(): Short`
* `toInt(): Int`
* `toLong(): Long`
* `toFloat(): Float`
* `toDouble(): Double`

很多情况都不需要显式类型转换，因为类型会从上下文推断出来，
而算术运算会有重载做适当转换，例如：

```kotlin
val l = 1L + 3 // Long + Int => Long
```

## 数字运算

Kotlin支持数字运算的标准集：`+`、 `-`、 `*`、 `/`、 `%`。它们已定义<!--
-->为相应的类成员：

```kotlin
fun main() {
//sampleStart
    println(1 + 2)
    println(2_500_000_000L - 1L)
    println(3.14 * 2.71)
    println(10.0 / 3)
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

还可以为自定义类覆盖这些操作符。详情请参见[操作符重载](operator-overloading.md)。

### 整数除法

整数间的除法总是返回整数。会丢弃任何小数部分。

```kotlin
fun main() {
//sampleStart
    val x = 5 / 2
    //println(x == 2.5) // ERROR: Operator '==' cannot be applied to 'Int' and 'Double'
    println(x == 2)
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

对于任何两个整数类型之间的除法来说都是如此：

```kotlin
fun main() {
//sampleStart
    val x = 5L / 2
    println(x == 2L)
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

如需返回浮点类型，请将其中的一个参数显式转换为浮点类型：

```kotlin
fun main() {
//sampleStart
    val x = 5 / 2.toDouble()
    println(x == 2.5)
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

### 位运算

Kotlin 对整数提供了一组*位运算*。它们直接使用数字的比特表示在<!--
-->二进制级别进行操作。
位运算有可以通过中缀形式调用的函数表示。只能应用于 `Int` 与 `Long`：

```kotlin
val x = (1 shl 2) and 0x000FF000
```

这是完整的位运算列表：

* `shl(bits)` – 有符号左移
* `shr(bits)` – 有符号右移
* `ushr(bits)` – 无符号右移
* `and(bits)` – 位**与**
* `or(bits)` – 位**或**
* `xor(bits)` – 位**异或**
* `inv()` – 位非

### 浮点数比较

本节讨论的浮点数操作如下：

* 相等性检测：`a == b` 与 `a != b`
* 比较操作符：`a < b`、 `a > b`、 `a <= b`、 `a >= b`
* 区间实例以及区间检测：`a..b`、 `x in a..b`、 `x !in a..b`

当其中的操作数 `a` 与 `b` 都是静态已知的 `Float` 或 `Double` 或者它们对应的可空类型（声明为<!--
-->该类型，或者推断为该类型，或者[智能类型转换](typecasts.html#智能转换)的结果是该类型），两数字所形成的操作<!--
-->或者区间遵循 [IEEE 754 浮点运算标准](https://zh.wikipedia.org/wiki/IEEE_754)。

然而，为了支持泛型场景并提供全序支持，当这些操作数**并非**静态类型为<!--
-->浮点数（例如是 `Any`、 `Comparable<……>`、 类型参数）时，这些操作使用<!--
-->为 `Float` 与 `Double` 实现的不符合标准的 `equals` 与 `compareTo`，这会出现：

* 认为 `NaN` 与其自身相等
* 认为 `NaN` 比包括正无穷大（`POSITIVE_INFINITY`）在内的任何其他元素都大
* 认为 `-0.0` 小于 `0.0`