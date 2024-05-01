[//]: # (title: 无符号整型)

除了[整数类型](numbers.md#整数类型)，对于无符号整数，Kotlin 还提供了以下类型：

| Type     | Size (bits) | Min value | Max value                                       |
|----------|-------------|-----------|-------------------------------------------------|
| `UByte`  | 8           | 0         | 255                                             |
| `UShort` | 16          | 0         | 65,535                                          |
| `UInt`   | 32          | 0         | 4,294,967,295 (2<sup>32</sup> - 1)              |
| `ULong`  | 64          | 0         | 18,446,744,073,709,551,615 (2<sup>64</sup> - 1) |


无符号类型支持其对应有符号类型的大多数操作。

> 无符号数实现为具有单个存储属性的[内联类](inline-classes.md)， 
> 该属性类型为与其宽度相同的对应有符号类型。 If you want to convert between unsigned and signed integer types,
> make sure you update your code so that any function calls and operations support the new type.
>
{type="note"}

## 无符号数组与区间

> 无符号数组及其对应操作处于 [Beta](components-stability.md) 状态。 它们可能随时进行不兼容变更。
> 需要选择加入（详见下文）。
>
{type="warning"}

与原生类型相同，每个无符号类型都有表示相应类型数组的类型：

* `UByteArray`: 无符号字节数组。
* `UShortArray`: 无符号短整型数组。
* `UIntArray`: 无符号整型数组。
* `ULongArray`: 无符号长整型数组。

与有符号整型数组一样，它们提供了类似于 `Array` 类的 API 而没有装箱开销。

When you use unsigned arrays, you receive a warning that indicates that this feature is not stable yet.
To remove the warning, opt-in with the `@ExperimentalUnsignedTypes` annotation.
It's up to you to decide if your clients have to explicitly opt-in into usage of your API, but keep in mind that unsigned
arrays are not a stable feature, so an API that uses them can be broken by changes in the language.
[Learn more about opt-in requirements](opt-in-requirements.md).

[区间与数列](ranges.md)也支持 `UInt` 与 `ULong`（通过这些类 `UIntRange`、 `UIntProgression`、
`ULongRange`、 `ULongProgression`）。 Together with the unsigned integer types, these classes are stable.

## 无符号整数字面值

为使无符号整型更易于使用，Kotlin 提供了用后缀标记整型字面值来<!--
-->表示指定无符号类型（类似于 `Float` 或 `Long`）：

* `u` and `U` tag is for unsigned literals. The exact type is determined based on the expected type.
  If no expected type is provided, compiler will use `UInt` or `ULong` depending on the size of literal:

  ```kotlin
  val b: UByte = 1u  // UByte，已提供预期类型
  val s: UShort = 1u // UShort，已提供预期类型
  val l: ULong = 1u  // ULong，已提供预期类型
  
  val a1 = 42u // UInt：未提供预期类型，常量适于 UInt
  val a2 = 0xFFFF_FFFF_FFFFu // ULong：未提供预期类型，常量不适于 UInt
  ```

* `uL` 与 `UL` 显式将字面值标记为无符号长整型：

  ```kotlin
  val a = 1UL // ULong，即使未提供预期类型并且常量适于 UInt
  ```

## 使用场景

The main use case of unsigned numbers is utilizing the full bit range of an integer to represent positive values.  
For example, to represent hexadecimal constants that do not fit in signed types such as color in 32-bit `AARRGGBB` format:

```kotlin
data class Color(val representation: UInt)

val yellow = Color(0xFFCC00CCu)
```

You can use unsigned numbers to initialize byte arrays without explicit `toByte()` literal casts:

```kotlin
val byteOrderMarkUtf8 = ubyteArrayOf(0xEFu, 0xBBu, 0xBFu)
```

Another use case is interoperability with native APIs. Kotlin allows representing native declarations that contain 
unsigned types in the signature. The mapping won't substitute unsigned integers with signed ones keeping the semantics unaltered.

### 非目标

While unsigned integers can only represent positive numbers and zero, it's not a goal to use them where application 
domain requires non-negative integers. For example, as a type of collection size or collection index value.

There are a couple of reasons:

* Using signed integers can help to detect accidental overflows and signal error conditions, such as 
  [`List.lastIndex`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/last-index.html) being -1 for an empty list.
* Unsigned integers cannot be treated as a range-limited version of signed ones because their range of values is not a 
  subset of the signed integers range. Neither signed, nor unsigned integers are subtypes of each other.