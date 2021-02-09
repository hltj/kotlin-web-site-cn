---
type: doc
layout: reference
category: "Basics"
title: "基本语法"
---

# 基本语法

{:#defining-packages}

## 包的定义与导入

包的声明应处于源文件顶部：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
package my.demo

import kotlin.text.*

// ……
```

</div>

目录与包的结构无需匹配：源代码可以在文件系统的任意位置。

参见[包](packages.html)。

## 程序入口点

Kotlin 应用程序的入口点是 `main` 函数。

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

```kotlin
fun main() {
    println("Hello world!")
}
```

</div>

{:#defining-functions}

## 函数

带有两个 `Int` 参数、返回 `Int` 的函数：

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

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

</div>

将表达式作为函数体、返回值类型自动推断的函数：

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

```kotlin
//sampleStart
fun sum(a: Int, b: Int) = a + b
//sampleEnd

fun main() {
    println("sum of 19 and 23 is ${sum(19, 23)}")
}
```

</div>

函数返回无意义的值：

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

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

</div>

`Unit` 返回类型可以省略：

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

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

</div>

参见[函数](functions.html)。

{:#defining-variables}

## 变量

定义只读局部变量使用关键字 `val` 定义。只能为其赋值一次。

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

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

</div>

可重新赋值的变量使用 `var` 关键字：

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

```kotlin
fun main() {
//sampleStart
    var x = 5 // 自动推断出 `Int` 类型
    x += 1
//sampleEnd
    println("x = $x")
}
```

</div>

顶层变量：

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

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

</div>

参见[属性](properties.html)。


## 注释

与大多数现代语言一样，Kotlin 支持单行（或*行末*）与多行（*块*）注释。

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
// 这是一个行注释

/* 这是一个多行的
   块注释。 */
```

</div>

Kotlin 中的块注释可以嵌套。

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
/* 注释从这里开始
/* 包含嵌套的注释 */     
并且在这里结束。 */
```

</div>

参见[编写 Kotlin 代码文档](kotlin-doc.html) 查看关于文档注释语法的信息。

{:#using-string-templates}

## 字符串模板

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

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

</div>

参见[字符串模板](basic-types.html#字符串模板)。

{:#using-conditional-expressions}

## 条件表达式

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

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

</div>


在 Kotlin 中，*if*{: .keyword } 也可以用作表达式：

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

```kotlin
//sampleStart
fun maxOf(a: Int, b: Int) = if (a > b) a else b
//sampleEnd

fun main() {
    println("max of 0 and 42 is ${maxOf(0, 42)}")
}
```

</div>

参见[*if*{: .keyword } 表达式](control-flow.html#if-表达式)。

{:#using-nullable-values-and-checking-for-null}

## 空值与 *null*{: .keyword } 检测

当某个变量的值可以为 *null*{: .keyword } 的时候，必须在声明处的类型后添加 `?` 来标识该引用可为空。

如果 `str` 的内容不是数字返回 *null*{: .keyword }：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
fun parseInt(str: String): Int? {
    // ……
}
```

</div>

使用返回可空值的函数:

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

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

</div>

或者


<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

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

</div>

参见[空安全](null-safety.html)。

{:#using-type-checks-and-automatic-casts}

## 类型检测与自动类型转换

*is*{: .keyword } 运算符检测一个表达式是否某类型的一个实例。
如果一个不可变的局部变量或属性已经判断出为某类型，那么检测后的分支中可以直接当作该类型使用，无需显式转换：


<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

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
        println("'$obj' string length is ${getStringLength(obj) ?: "... err, not a string"} ")
    }
    printLength("Incomprehensibilities")
    printLength(1000)
    printLength(listOf(Any()))
}
```

</div>

或者

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

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
        println("'$obj' string length is ${getStringLength(obj) ?: "... err, not a string"} ")
    }
    printLength("Incomprehensibilities")
    printLength(1000)
    printLength(listOf(Any()))
}
```

</div>

甚至

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

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
        println("'$obj' string length is ${getStringLength(obj) ?: "... err, is empty or not a string at all"} ")
    }
    printLength("Incomprehensibilities")
    printLength("")
    printLength(1000)
}
```

</div>

参见[类](classes.html)以及[类型转换](typecasts.html)。

{:#using-a-for-loop}

## `for` 循环

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

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

</div>

或者

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

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

</div>


参见 [for 循环](control-flow.html#for-循环)。

{:#using-a-while-loop}

## `while` 循环

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

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

</div>


参见 [while 循环](control-flow.html#while-循环)。

{:#using-when-expression}

## `when` 表达式

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

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

</div>


参见 [when 表达式](control-flow.html#when-表达式)。

{:#using-ranges}

## 使用区间（range）

使用 *in*{: .keyword } 运算符来检测某个数字是否在指定区间内：

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

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

</div>


检测某个数字是否在指定区间外:

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

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

</div>

区间迭代:

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

```kotlin
fun main() {
//sampleStart
    for (x in 1..5) {
        print(x)
    }
//sampleEnd
}
```

</div>

或数列迭代：

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

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

</div>

参见[区间](ranges.html)。

{:#using-collections}

## 集合

对集合进行迭代:

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

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

</div>

使用 *in*{: .keyword } 运算符来判断集合内是否包含某实例：

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

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

</div>

使用 lambda 表达式来过滤（filter）与映射（map）集合：

<div class="sample" markdown="1" theme="idea" auto-indent="false" indent="2">

```kotlin
fun main() {
//sampleStart
    val fruits = listOf("banana", "avocado", "apple", "kiwifruit")
    fruits
      .filter { it.startsWith("a") }
      .sortedBy { it }
      .map { it.toUpperCase() }
      .forEach { println(it) }
//sampleEnd
}
```

</div>

参见[集合概述](collections-overview.html)。

## 创建基本类及其实例

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

```kotlin
fun main() {
//sampleStart
    val rectangle = Rectangle(5.0, 2.0)
    val triangle = Triangle(3.0, 4.0, 5.0)
//sampleEnd
    println("Area of rectangle is ${rectangle.calculateArea()}, its perimeter is ${rectangle.perimeter}")
    println("Area of triangle is ${triangle.calculateArea()}, its perimeter is ${triangle.perimeter}")
}

abstract class Shape(val sides: List<Double>) {
    val perimeter: Double get() = sides.sum()
    abstract fun calculateArea(): Double
}

interface RectangleProperties {
    val isSquare: Boolean
}

class Rectangle(
    var height: Double,
    var length: Double
) : Shape(listOf(height, length, height, length)), RectangleProperties {
    override val isSquare: Boolean get() = length == height
    override fun calculateArea(): Double = height * length
}

class Triangle(
    var sideA: Double,
    var sideB: Double,
    var sideC: Double
) : Shape(listOf(sideA, sideB, sideC)) {
    override fun calculateArea(): Double {
        val s = perimeter / 2
        return Math.sqrt(s * (s - sideA) * (s - sideB) * (s - sideC))
    }
}
```

</div>

参见[类](classes.html)以及[对象与实例](object-declarations.html)。
