[//]: # (title: 空安全)

## 可空类型与非空类型

Kotlin 的类型系统旨在消除来自代码空引用的危险，也称为[《十亿美元的错误》](https://en.wikipedia.org/wiki/Tony_Hoare#Apologies_and_retractions)。

许多编程语言（包括 Java）中最常见的陷阱之一，就是访问空引用的成员<!--
-->会导致空引用异常。在 Java 中，这等同于 `NullPointerException` 
或简称 *NPE*。

Kotlin 中 NPE 的可能的原因只可能是：

* 显式调用 `throw NullPointerException()`。
* 使用了下文描述的 `!!` 操作符。
* 数据在初始化时不一致，例如当：
  * 传递一个在构造函数中出现的未初始化的 `this` 并用于其他地方（“泄漏 `this`”）。
  * [超类的构造函数调用一个开放成员](inheritance.md#派生类初始化顺序)，该成员在派生中类的实现<!--
  -->使用了未初始化的状态。
* Java 互操作：
  * 企图访问[平台类型](java-interop.md#空安全与平台类型)的 `null` 引用的成员；
  * 用于 Java 互操作的泛型类型的可空性问题，例如一段 Java 代码可能会向 Kotlin 的 `MutableList<String>` 中加入
  `null`，就需要一个 `MutableList<String?>` 才能处理。
  * 由外部 Java 代码引发的其他问题。

在 Kotlin 中，类型系统区分一个引用可以容纳 `null` （可空引用）还是<!--
-->不能容纳（非空引用）。
例如，String 类型的常规变量不能容纳 `null`：

```kotlin
fun main() {
//sampleStart
    var a: String = "abc" // 默认情况下，常规初始化意味着非空
    a = null // 编译错误
//sampleEnd
}
```
{kotlin-runnable="true" validate="false"}

如果要允许为空，可以声明一个变量为可空字符串（写作 `String?`）：

```kotlin
fun main() {
//sampleStart
    var b: String? = "abc" // 可以设置为空
    b = null // ok
    print(b)
//sampleEnd
}
```
{kotlin-runnable="true"}

现在，如果你调用 `a` 的方法或者访问它的属性，它保证不会导致 `NPE`，这样你就可以放心地使用：

```kotlin
val l = a.length
```

但是如果你想访问 `b` 的同一个属性，那么这是不安全的，并且编译器会报告一个错误：

```kotlin
val l = b.length // 错误：变量“b”可能为空
```

但是，还是需要访问该属性，对吧？有几种方式可以做到。

## 在条件中检测 `null`

首先，你可以显式检测 `b` 是否为 `null`，并分别处理两种可能：

```kotlin
val l = if (b != null) b.length else -1
```

编译器会跟踪所执行检测的信息，并允许你在 `if` 内部调用 `length`。
同时，也支持更复杂（更智能）的条件：

```kotlin
fun main() {
//sampleStart
    val b: String? = "Kotlin"
    if (b != null && b.length > 0) {
        print("String of length ${b.length}")
    } else {
        print("Empty string")
    }
//sampleEnd
}
```
{kotlin-runnable="true"}

请注意，这只适用于 `b` 是不可变的情况（即在检测与使用之间没有修改过的局部变量
，或是有幕后字段且不可覆盖的 `val` 成员），因为否则可能会发生<!--
-->在检测之后 `b` 又变为 `null` 的情况。

## 安全的调用

Your second option for accessing a property on a nullable variable is using the safe call operator `?.`:

```kotlin
fun main() {
//sampleStart
    val a = "Kotlin"
    val b: String? = null
    println(b?.length)
    println(a?.length) // 无需安全调用
//sampleEnd
}
```
{kotlin-runnable="true"}

如果 `b` 非空，就返回 `b.length`，否则返回 `null`，这个表达式的类型是 `Int?`。

安全调用在链式调用中很有用。例如，一个员工 Bob 可能会（或者不会）分配给一个部门。
可能有另外一个员工是该部门的负责人。获取 Bob 所在部门负责人（如果有的话）的名字，
写作：

```kotlin
bob?.department?.head?.name
```

如果任意一个属性（环节）为 `null`，这个链式调用就会返回 `null`。

如果要只对非空值执行某个操作，安全调用操作符可以与
[`let`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/let.html) 一起使用：

```kotlin
fun main() {
//sampleStart
    val listWithNulls: List<String?> = listOf("Kotlin", null)
    for (item in listWithNulls) {
         item?.let { println(it) } // 输出 Kotlin 并忽略 null
    }
//sampleEnd
}
```
{kotlin-runnable="true"}

安全调用也可以出现在赋值的左侧。这样，如果调用链中的任何一个接收者为
`null` 都会跳过赋值，而右侧的表达式根本不会求值：

```kotlin
// 如果 `person` 或者 `person.department` 其中之一为空，都不会调用该函数：
person?.department?.head = managersPool.getManager()
```

## Elvis 操作符

当有一个可空的引用 `b` 时，可以说“如果 `b` 不是 `null`，就使用它；否则使用某个非空的值”：

```kotlin
val l: Int = if (b != null) b.length else -1
```

Instead of writing the complete `if` expression, you can also express this with the Elvis operator `?:`:

```kotlin
val l = b?.length ?: -1
```

如果 `?:` 左侧表达式不是 `null`，Elvis 操作符就返回其左侧表达式，否则返回<!--
-->右侧表达式。
请注意，当且仅当左侧为 `null` 时，才会对右侧表达式求值。

因为 `throw` 和 `return` 在 Kotlin 中都是表达式，所以它们也可以用在
elvis 操作符右侧。这可能会很方便，例如，检测函数参数：

```kotlin
fun foo(node: Node): String? {
    val parent = node.getParent() ?: return null
    val name = node.getName() ?: throw IllegalArgumentException("name expected")
    // ……
}
```

## `!!` 操作符

第三种选择是为 NPE 爱好者准备的：非空断言运算符（`!!`）将任何值转换为非空<!--
-->类型，若该值为 `null` 则抛出异常。可以写 `b!!` ，这会返回一个非空的 `b` 值
（例如：在我们示例中的 `String`）或者如果 `b` 为 `null`，就会抛出一个 `NPE` 异常：

```kotlin
val l = b!!.length
```

因此，如果你想要一个 NPE，你可以得到它，但是你必须显式要求它，否则它不会不期而至。

## 安全的类型转换

如果对象不是目标类型，那么常规类型转换可能会导致 `ClassCastException`。
另一个选择是使用安全的类型转换，如果尝试转换不成功则返回 `null`：

```kotlin
val aInt: Int? = a as? Int
```

## 可空类型的集合

如果你有一个可空类型元素的集合，并且想要过滤非空元素，你可以使用
`filterNotNull` 来实现：

```kotlin
val nullableList: List<Int?> = listOf(1, 2, null, 4)
val intList: List<Int> = nullableList.filterNotNull()
```
