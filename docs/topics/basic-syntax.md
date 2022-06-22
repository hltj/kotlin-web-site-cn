[//]: # (title: 基本语法)

这是一组基本语法元素及示例。在每段的末尾都有<!--
-->一个指向相关主题详述的链接。

也可以通过 JetBrains 学院上的免费 [Kotlin 基础课程](https://hyperskill.org/join/fromdocstoJetSalesStat?redirect=true&next=/tracks/18)学习所有
Kotlin 要领。

## 包的定义与导入

包的声明应处于源文件顶部。

```kotlin
package my.demo

import kotlin.text.*

// ……
```

目录与包的结构无需匹配：源代码可以在文件系统的任意位置。

参见[包](packages.md)。

## 程序入口点

Kotlin 应用程序的入口点是 `main` 函数。

```kotlin
fun main() {
    println("Hello world!")
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

`main` 的另一种形式接受可变数量的 `String` 参数。 

```kotlin
fun main(args: Array<String>) {
    println(args.contentToString())
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

## 输出打到标准输出

`print` 将其参数打到标准输出。

```kotlin
fun main() {
//sampleStart
    print("Hello ")
    print("world!")
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

`println` 输出其参数并添加换行符，以便接下来输出的内容出现在下一行。

```kotlin
fun main() {
//sampleStart
    println("Hello world!")
    println(42)
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

## 函数

带有两个 `Int` 参数、返回 `Int` 的函数。

```kotlin
//sampleStart
fun sum(a: Int, b: Int): Int {
    return a + b
}
//sampleEnd

fun main() {
    print("sum of 3 and 5 is ")
    println(sum(3, 5))
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

函数体可以是表达式。其返回类型可以推断出来。

```kotlin
//sampleStart
fun sum(a: Int, b: Int) = a + b
//sampleEnd

fun main() {
    println("sum of 19 and 23 is ${sum(19, 23)}")
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

返回无意义的值的函数。

```kotlin
//sampleStart
fun printSum(a: Int, b: Int): Unit {
    println("sum of $a and $b is ${a + b}")
}
//sampleEnd

fun main() {
    printSum(-1, 8)
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

`Unit` 返回类型可以省略。

```kotlin
//sampleStart
fun printSum(a: Int, b: Int) {
    println("sum of $a and $b is ${a + b}")
}
//sampleEnd

fun main() {
    printSum(-1, 8)
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

参见[函数](functions.md)。

## 变量

定义只读局部变量使用关键字 `val` 定义。只能为其赋值一次。

```kotlin
fun main() {
//sampleStart
    val a: Int = 1  // 立即赋值
    val b = 2   // 自动推断出 `Int` 类型
    val c: Int  // 如果没有初始值类型不能省略
    c = 3       // 明确赋值
//sampleEnd
    println("a = $a, b = $b, c = $c")
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

可重新赋值的变量使用 `var` 关键字。

```kotlin
fun main() {
//sampleStart
    var x = 5 // 自动推断出 `Int` 类型
    x += 1
//sampleEnd
    println("x = $x")
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

可以在顶层声明变量。

```kotlin
//sampleStart
val PI = 3.14
var x = 0

fun incrementX() { 
    x += 1 
}
//sampleEnd

fun main() {
    println("x = $x; PI = $PI")
    incrementX()
    println("incrementX()")
    println("x = $x; PI = $PI")
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

参见[属性](properties.md)。

## 创建类与实例

使用 `class` 关键字定义类。
```kotlin
class Shape
```

类的属性可以在其声明或主体中列出。 

```kotlin
class Rectangle(var height: Double, var length: Double) {
    var perimeter = (height + length) * 2 
}
```

具有类声明中所列参数的默认构造函数会自动可用。 

```kotlin
class Rectangle(var height: Double, var length: Double) {
    var perimeter = (height + length) * 2 
}
fun main() {
//sampleStart
    val rectangle = Rectangle(5.0, 2.0)
    println("The perimeter is ${rectangle.perimeter}")
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

类之间继承由冒号（`:`）声明。默认情况下类都是 final 的；如需让一个类可继承， 
请将其标记为 `open`。

```kotlin
open class Shape

class Rectangle(var height: Double, var length: Double): Shape() {
    var perimeter = (height + length) * 2 
}
```

参见[类](classes.md)以及[对象与实例](object-declarations.md)。

## 注释

与大多数现代语言一样，Kotlin 支持单行（或*行末*）与多行（*块*）注释。

```kotlin
// 这是一个行注释

/* 这是一个多行的
   块注释。 */
```

Kotlin 中的块注释可以嵌套。

```kotlin
/* 注释从这里开始
/* 包含嵌套的注释 *&#8288;/     
并且在这里结束。 */
```

参见[编写 Kotlin 代码文档](kotlin-doc.md) 查看关于文档注释语法的信息。

## 字符串模板

```kotlin
fun main() {
//sampleStart
    var a = 1
    // 模板中的简单名称：
    val s1 = "a is $a" 
    
    a = 2
    // 模板中的任意表达式：
    val s2 = "${s1.replace("is", "was")}, but now is $a"
//sampleEnd
    println(s2)
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

参见[字符串模板](basic-types.md#字符串模板)。

## 条件表达式

```kotlin
//sampleStart
fun maxOf(a: Int, b: Int): Int {
    if (a > b) {
        return a
    } else {
        return b
    }
}
//sampleEnd

fun main() {
    println("max of 0 and 42 is ${maxOf(0, 42)}")
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

在 Kotlin 中，`if` 也可以用作表达式。

```kotlin
//sampleStart
fun maxOf(a: Int, b: Int) = if (a > b) a else b
//sampleEnd

fun main() {
    println("max of 0 and 42 is ${maxOf(0, 42)}")
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

参见[`if` 表达式](control-flow.md#if-表达式)。

## for 循环

```kotlin
fun main() {
//sampleStart
    val items = listOf("apple", "banana", "kiwifruit")
    for (item in items) {
        println(item)
    }
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

或者

```kotlin
fun main() {
//sampleStart
    val items = listOf("apple", "banana", "kiwifruit")
    for (index in items.indices) {
        println("item at $index is ${items[index]}")
    }
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

参见 [for 循环](control-flow.md#for-循环)。

## while 循环

```kotlin
fun main() {
//sampleStart
    val items = listOf("apple", "banana", "kiwifruit")
    var index = 0
    while (index < items.size) {
        println("item at $index is ${items[index]}")
        index++
    }
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

参见 [while 循环](control-flow.md#while-循环)。

## when 表达式

```kotlin
//sampleStart
fun describe(obj: Any): String =
    when (obj) {
        1          -> "One"
        "Hello"    -> "Greeting"
        is Long    -> "Long"
        !is String -> "Not a string"
        else       -> "Unknown"
    }
//sampleEnd

fun main() {
    println(describe(1))
    println(describe("Hello"))
    println(describe(1000L))
    println(describe(2))
    println(describe("other"))
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

参见 [when 表达式](control-flow.md#when-表达式)。

## 使用区间（range）

使用 `in` 操作符来检测某个数字是否在指定区间内。

```kotlin
fun main() {
//sampleStart
    val x = 10
    val y = 9
    if (x in 1..y+1) {
        println("fits in range")
    }
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

检测某个数字是否在指定区间外。

```kotlin
fun main() {
//sampleStart
    val list = listOf("a", "b", "c")
    
    if (-1 !in 0..list.lastIndex) {
        println("-1 is out of range")
    }
    if (list.size !in list.indices) {
        println("list size is out of valid list indices range, too")
    }
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

区间迭代。

```kotlin
fun main() {
//sampleStart
    for (x in 1..5) {
        print(x)
    }
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

或数列迭代。

```kotlin
fun main() {
//sampleStart
    for (x in 1..10 step 2) {
        print(x)
    }
    println()
    for (x in 9 downTo 0 step 3) {
        print(x)
    }
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

参见[区间与数列](ranges.md)。

## 集合

对集合进行迭代。

```kotlin
fun main() {
    val items = listOf("apple", "banana", "kiwifruit")
//sampleStart
    for (item in items) {
        println(item)
    }
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

使用 `in` 操作符来判断集合内是否包含某实例。

```kotlin
fun main() {
    val items = setOf("apple", "banana", "kiwifruit")
//sampleStart
    when {
        "orange" in items -> println("juicy")
        "apple" in items -> println("apple is fine too")
    }
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

使用 lambda 表达式来过滤（filter）与映射（map）集合：

```kotlin
fun main() {
//sampleStart
    val fruits = listOf("banana", "avocado", "apple", "kiwifruit")
    fruits
      .filter { it.startsWith("a") }
      .sortedBy { it }
      .map { it.uppercase() }
      .forEach { println(it) }
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

参见[集合概述](collections-overview.md)。

## 空值与空检测

当可能用 `null` 值时，必须将引用显式标记为可空。可空类型名称以问号（`?`）结尾。

如果 `str` 的内容不是数字返回 `null`：

```kotlin
fun parseInt(str: String): Int? {
    // ……
}
```

使用返回可空值的函数：

```kotlin
fun parseInt(str: String): Int? {
    return str.toIntOrNull()
}

//sampleStart
fun printProduct(arg1: String, arg2: String) {
    val x = parseInt(arg1)
    val y = parseInt(arg2)

    // 直接使用 `x * y` 会导致编译错误，因为它们可能为 null
    if (x != null && y != null) {
        // 在空检测后，x 与 y 会自动转换为非空值（non-nullable）
        println(x * y)
    }
    else {
        println("'$arg1' or '$arg2' is not a number")
    }
}
//sampleEnd

fun main() {
    printProduct("6", "7")
    printProduct("a", "7")
    printProduct("a", "b")
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

或者

```kotlin
fun parseInt(str: String): Int? {
    return str.toIntOrNull()
}

fun printProduct(arg1: String, arg2: String) {
    val x = parseInt(arg1)
    val y = parseInt(arg2)
    
//sampleStart
    // ……
    if (x == null) {
        println("Wrong number format in arg1: '$arg1'")
        return
    }
    if (y == null) {
        println("Wrong number format in arg2: '$arg2'")
        return
    }

    // 在空检测后，x 与 y 会自动转换为非空值
    println(x * y)
//sampleEnd
}

fun main() {
    printProduct("6", "7")
    printProduct("a", "7")
    printProduct("99", "b")
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

参见[空安全](null-safety.md)。

## 类型检测与自动类型转换

`is` 操作符检测一个表达式是否某类型的一个实例。
如果一个不可变的局部变量或属性已经判断出为某类型，那么检测后的分支中可以直接当作该类型使用，无需显式转换：

```kotlin
//sampleStart
fun getStringLength(obj: Any): Int? {
    if (obj is String) {
        // `obj` 在该条件分支内自动转换成 `String`
        return obj.length
    }

    // 在离开类型检测分支后，`obj` 仍然是 `Any` 类型
    return null
}
//sampleEnd

fun main() {
    fun printLength(obj: Any) {
        println("Getting the length of '$obj'. Result: ${getStringLength(obj) ?: "Error: The object is not a string"} ")
    }
    printLength("Incomprehensibilities")
    printLength(1000)
    printLength(listOf(Any()))
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

或者

```kotlin
//sampleStart
fun getStringLength(obj: Any): Int? {
    if (obj !is String) return null

    // `obj` 在这一分支自动转换为 `String`
    return obj.length
}
//sampleEnd

fun main() {
    fun printLength(obj: Any) {
        println("Getting the length of '$obj'. Result: ${getStringLength(obj) ?: "Error: The object is not a string"} ")
    }
    printLength("Incomprehensibilities")
    printLength(1000)
    printLength(listOf(Any()))
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

甚至

```kotlin
//sampleStart
fun getStringLength(obj: Any): Int? {
    // `obj` 在 `&&` 右边自动转换成 `String` 类型
    if (obj is String && obj.length > 0) {
        return obj.length
    }

    return null
}
//sampleEnd

fun main() {
    fun printLength(obj: Any) {
        println("Getting the length of '$obj'. Result: ${getStringLength(obj) ?: "Error: The object is not a string"} ")
    }
    printLength("Incomprehensibilities")
    printLength("")
    printLength(1000)
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

参见[类](classes.md)以及[类型转换](typecasts.md)。

