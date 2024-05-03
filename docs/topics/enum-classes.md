[//]: # (title: 枚举类)

枚举类的最基本的应用场景是实现类型安全的枚举：

```kotlin
enum class Direction {
    NORTH, SOUTH, WEST, EAST
}
```
每个枚举常量都是一个对象。枚举常量以逗号分隔。

因为每一个枚举都是枚举类的实例，所以可以这样初始化：

```kotlin
enum class Color(val rgb: Int) {
    RED(0xFF0000),
    GREEN(0x00FF00),
    BLUE(0x0000FF)
}
```

## 匿名类

枚举常量可以声明其带有相应方法以及覆盖了基类方法的自身匿名类
。

```kotlin
enum class ProtocolState {
    WAITING {
        override fun signal() = TALKING
    },

    TALKING {
        override fun signal() = WAITING
    };

    abstract fun signal(): ProtocolState
}
```

如果枚举类定义任何成员，那么使用分号将成员定义与常量定义分隔开。

## 在枚举类中实现接口

一个枚举类可以实现接口（但不能从类继承），可以为所有条目提供<!--
-->统一的接口成员实现，也可以在相应匿名类中为每个条目提供各自的实现。
只需将想要实现的接口添加到枚举类声明中即可，如下所示：

```kotlin
import java.util.function.BinaryOperator
import java.util.function.IntBinaryOperator

//sampleStart
enum class IntArithmetics : BinaryOperator<Int>, IntBinaryOperator {
    PLUS {
        override fun apply(t: Int, u: Int): Int = t + u
    },
    TIMES {
        override fun apply(t: Int, u: Int): Int = t * u
    };
    
    override fun applyAsInt(t: Int, u: Int) = apply(t, u)
}
//sampleEnd

fun main() {
    val a = 13
    val b = 31
    for (f in IntArithmetics.entries) {
        println("$f($a, $b) = ${f.apply(a, b)}")
    }
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.9"}

All enum classes implement the [Comparable](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-comparable/index.html)
interface by default. Constants in the enum class are defined in the natural order. For more information, see [Ordering](collection-ordering.md).

## 使用枚举常量

Kotlin 中的枚举类有合成的属性与方法用于列出定义的枚举常量以及通过<!--
-->其名称获取枚举常量。这些方法的签名如下（假设枚举类的名称是 `EnumClass`）：

```kotlin
EnumClass.valueOf(value: String): EnumClass
EnumClass.entries: EnumEntries<EnumClass> // specialized List<EnumClass>
```

Below is an example of them in action:

```kotlin
enum class RGB { RED, GREEN, BLUE }

fun main() {
    for (color in RGB.entries) println(color.toString()) // prints RED, GREEN, BLUE
    println("The first color is: ${RGB.valueOf("RED")}") // prints "The first color is: RED"
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.9" id="rgb-enums-kotlin"}

如果指定的名称与类中定义的任何枚举常量均不匹配，
`valueOf()` 方法会抛出 `IllegalArgumentException` 异常。

Prior to the introduction of `entries` in Kotlin 1.9.0, the `values()` function was used to retrieve an array of enum constants.

每个枚举常量也都具有这两个属性：[`name`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-enum/name.html)
与 [`ordinal`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-enum/ordinal.html)， 用于在枚举类声明中获取其名称<!--
-->与（自 0 起的）位置：

```kotlin
enum class RGB { RED, GREEN, BLUE }

fun main() {
    //sampleStart
    println(RGB.RED.name)    // prints RED
    println(RGB.RED.ordinal) // prints 0
    //sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="rgb-enums-properties-kotlin"}

可以使用 [`enumValues<T>()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/enum-values.html) 与 [`enumValueOf<T>()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/enum-value-of.html) 函数<!--
-->以泛型的方式访问枚举类中的常量：

```kotlin
enum class RGB { RED, GREEN, BLUE }

inline fun <reified T : Enum<T>> printAllValues() {
    println(enumValues<T>().joinToString { it.name })
}

printAllValues<RGB>() // 输出 RED, GREEN, BLUE
```

> For more information about inline functions and reified type parameters, see [Inline functions](inline-functions.md).
>
> {type="tip"}
 
In Kotlin 1.9.20, the `enumEntries<T>()` function is introduced as a future replacement for the [`enumValues<T>()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/enum-values.html)
function.

The `enumValues<T>()` function is still supported, but we recommend that you use the `enumEntries<T>()` function instead
because it has less performance impact. Every time you call `enumValues<T>()` a new array is created, whereas whenever
you call `enumEntries<T>()` the same list is returned each time, which is far more efficient.

For example:

```kotlin
enum class RGB { RED, GREEN, BLUE }

@OptIn(ExperimentalStdlibApi::class)
inline fun <reified T : Enum<T>> printAllValues() {
    println(enumEntries<T>().joinToString { it.name })
}

printAllValues<RGB>() 
// RED, GREEN, BLUE
```

> The `enumEntries<T>()` function is Experimental. To use it, opt in with `@OptIn(ExperimentalStdlibApi)`, and
> [set the language version to at least 1.9](gradle-compiler-options.md#jvm-与-js-的公共属性).
>
{type="warning"}
