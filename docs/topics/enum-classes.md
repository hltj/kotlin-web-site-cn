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
    for (f in IntArithmetics.values()) {
        println("$f($a, $b) = ${f.apply(a, b)}")
    }
}
```
{kotlin-runnable="true"}

All enum classes implement the [Comparable](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-comparable/index.html)
interface by default. Constants in the enum class are defined in the natural order. For more information, see [Ordering](collection-ordering.md).

## 使用枚举常量

Kotlin 中的枚举类也有合成方法用于列出<!--
-->定义的枚举常量以及通过名称获取枚举常量。这些方法的<!--
-->签名如下（假设枚举类的名称是 `EnumClass`）：

```kotlin
EnumClass.valueOf(value: String): EnumClass
EnumClass.values(): Array<EnumClass>
```

如果指定的名称与类中定义的任何枚举常量均不匹配，
`valueOf()` 方法会抛出 `IllegalArgumentException` 异常。

可以使用 `enumValues<T>()` 与 `enumValueOf<T>()` 函数<!--
-->以泛型的方式访问枚举类中的常量：

```kotlin
enum class RGB { RED, GREEN, BLUE }

inline fun <reified T : Enum<T>> printAllValues() {
    print(enumValues<T>().joinToString { it.name })
}

printAllValues<RGB>() // 输出 RED, GREEN, BLUE
```

每个枚举常量都具有在枚举类声明中获取其名称与（自 0 起的）位置的属性：

```kotlin
val name: String
val ordinal: Int
```