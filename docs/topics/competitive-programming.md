[//]: # (title: Kotlin 用于竞技程序设计)

本教程适用于之前未使用 Kotlin
的竞技程序员，也适用于之前从未参与过任何竞技性程序设计活动的 Kotlin 开发人员。
本教程假定读者具有相应的编程技能。

[竞技性程序设计](https://en.wikipedia.org/wiki/Competitive_programming)<!--
-->是一项智力运动，参赛选手在严格的限制条件下编写程序精确地解决指定的<!--
-->算法问题。问题可以简单到<!--
-->任何软件开发人员都能解题、只需很少代码就能得到正确答案，也可以复杂到需要<!--
-->特殊的算法、数据结构知识以及大量实践。虽然 Kotlin 不是专为竞技性<!--
-->编程而设计的，但是它恰好适合这一领域，显著减少了<!--
-->程序员所需编写与阅读的样板代码量，这样几乎可以像动态<!--
-->脚本语言一样编写代码，同时又有静态类型语言的工具与性能支持。

关于如何搭建 Kotlin 开发环境，请参见[Get started with Kotlin/JVM](jvm-get-started.md)<!--
-->。在竞技程序设计中，通常会创建单个项目，而每个问题的答案<!--
-->写在单个源文件中。

## 简单示例：可达数问题

我们来看一个具体的示例。

[Codeforces](https://codeforces.com/) 
第 555 轮第 3 次分赛已于 4 月 26 日举行，意味着它有适合任何开发者尝试的问题。
可以打开[这个链接](https://codeforces.com/contest/1157)来阅读问题。
这组问题中最简单的是<!--
-->[问题 A：可达数](https://codeforces.com/contest/1157/problem/A)。
它要求实现问题陈述中所描述的简单算法。

我们会通过创建一个任意名称的 Kotlin 源文件来解这个问题。`A.kt` 就挺好。
首先，我们需要实现问题陈述中指定的函数，如下：

我们以这样的方式来表示函数 f(x)：将 x 加 1，然后，如果得到的数至少以一个零结尾，
就去掉这个零。

Kotlin 是一门实用且不拘一格的语言，既支持命令式也支持函数式编程风格，
而不会将开发人员推向任何一种风格。可以按函数式风格实现函数 `f`，使用像<!--
-->[尾递归](functions.md#尾递归函数)这样的 Kotlin 特性：

```kotlin
tailrec fun removeZeroes(x: Int): Int =
    if (x % 10 == 0) removeZeroes(x / 10) else x
    
fun f(x: Int) = removeZeroes(x + 1)
```

也可以编写函数 `f` 的命令式实现，使用传统的
[while 循环](control-flow.md)与可变变量（在 Kotlin 中用
[var](basic-syntax.md#变量) 表示）：

```kotlin
fun f(x: Int): Int {
    var cur = x + 1
    while (cur % 10 == 0) cur /= 10
    return cur
}
```

由于普遍使用类型推断，在 Kotlin 中很多地方的类型都是可选的，不过每个声明仍然具有<!--
-->编译期已知的明确定义的静态类型。

现在就只剩编写读取输入的主函数并实现问题<!--
-->陈述所要求算法的其余部分——计算在对标准输入所给出的初始数
`n` 重复应用函数 `f` 时所产生的不同整数的个数。

默认情况下，Kotlin 在 JVM 上运行，可以直接访问丰富且高效的集合库，其中包含<!--
-->通用的集合与数据结构，如动态大小的数组（`ArrayList`）、
基于哈希的 map 与 set（`HashMap`/`HashSet`）、基于树的 map 与 set（`TreeMap`/`TreeSet`）等。
使用整数哈希 set 来跟踪应用函数 `f` 时已达到的值，
该问题解法的一个简单命令式版本可以这样编写：

```kotlin
fun main() {
    var n = readln().toInt() // 读取输入的整数
    val reached = HashSet<Int>() // 可变的哈希 set
    while (reached.add(n)) n = f(n) // 迭代函数 f
    println(reached.size) // 输出答案
}
```

> The readln() function is available since [Kotlin 1.6.0](whatsnew16.md#新版-readline-函数).
>
{type="note"}

在竞技程序设计中无需处理输入格式错误的情况。 竞技程序设计中的输入格式<!--
-->向来都是精确指定的，并且实际输入不能偏离问题陈述中的输入规范<!--
-->。 That's why we're using Kotlin's [`readln()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.io/readln.html) function. 它断言输入的字符串存在，
如不存在则抛出异常。 同样，如果输入不是整数，那么 [`String.toInt()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/to-int.html)
函数会抛出异常。

所有在线竞技程序设计活动都允许使用预编写代码，因此可以定义自己的<!--
-->面向竞技程序设计的工具函数库，以使实际解题代码更易<!--
-->于读写。然后，可以使用该代码作为解题模板。例如，可以定义<!--
-->以下辅助函数来读取竞技程序设计中的输入：

```kotlin
private fun readInt() = readln().toInt()
private fun readStr() = readln().toString()
// 用于在解题中会用到的其他类型的类似声明等
```

请注意这里使用了 `private`（私有）[可见修饰符](visibility-modifiers.md)。
虽然可见性修饰符的概念与竞技程序设计并无瓜葛，
但是它让你能够将<!--
-->基于相同模板的多个解题文件放在同一包中，而不会出现公有声明冲突的报错。

## 函数式操作示例：长数问题

对于更复杂的问题，Kotlin 丰富的集合函数式操作库就派上用场了，
可以大幅减少模板代码，并将代码写成从上到下、从左到右的流式数据转换<!--
-->流水线。例如<!--
-->[问题 B：长数](https://codeforces.com/contest/1157/problem/B)问题<!--
-->用一个简单的贪心算法实现，可以采用这种风格编写而无需任何可变变量：

```kotlin
fun main() {
    // 读取输入
    val n = readln().toInt()
    val s = readln()
    val fl = readln().split(" ").map { it.toInt() }
    // 定义局部函数 f
    fun f(c: Char) = '0' + fl[c - '1']
    // 贪婪查找第一个与最后一个索引
    val i = s.indexOfFirst { c -> f(c) > c }
        .takeIf { it >= 0 } ?: s.length
    val j = s.withIndex().indexOfFirst { (j, c) -> j > i && f(c) < c }
        .takeIf { it >= 0 } ?: s.length
    // 组合并写出答案
    val ans =
        s.substring(0, i) +
        s.substring(i, j).map { c -> f(c) }.joinToString("") +
        s.substring(j)
    println(ans)
}
```

在这段密集的代码中，除了集合转换之外，还可以看到像局部函数<!--
-->以及 [elvis 操作符](null-safety.md#elvis-操作符) `?:` 这样灵便的 Kotlin 特性，
通过 elvis 操作符，可以用<!--
-->简洁易读的表达式如 `.takeIf { it >= 0 } ?: s.length`
来表达类似“如果是正数就取其值，否则取长度”的[惯用法](idioms.md)，
当然，也完全可以使用 Kotlin 创建额外的可变变量并以命令式风格表达相同的代码。

为了让这种竞技程序设计任务中读取输入更简洁，
可以使用以下输入读取辅助函数列表：

```kotlin
private fun readInt() = readln().toInt() // 单个整数
private fun readStrings() = readln().split(" ") // 字符串列表
private fun readInts() = readStrings().map { it.toInt() } // 整数列表
```

有了这些辅助函数，读取输入的代码部分变得更简单，一行行地严格遵循<!--
-->问题陈述中的输入规范：

```kotlin
    // 读取输入
    val n = readInt()
    val s = readln()
    val fl = readInts()
```

请注意，在竞技程序设计中，习惯给变量取比<!--
-->通常在工业编程实践中更短的名称，因为代码只需编写一次，以后就不用支持了。
当然，这些名称通常仍然是助记手段——数组用 `a`，
索引用 `i`、`j`，表格的行列号用 `r`、`c`，坐标用 `x`、`y` 等。
输入数据的名称保持与问题陈述中所给出的名称相同也更容易。
当然，越复杂的问题就越需要更多的代码来解，这导致更长、具有自解释性的<!--
-->变量名与函数名。

## 更多提示与技巧

竞技程序设计通常有这样的输入：

输入的第一行包含两个整数 `n` 与 `k`

在 Kotlin 中，这一行可以通过使用对整型列表进行<!--
-->[解构声明](destructuring-declarations.md)<!--
-->的下列语句简明地解析：

```kotlin
val (n, k) = readInts() 
```

很多人习惯使用 JVM 的 `java.util.Scanner` 类来解析结构较少的<!--
-->输入格式。Kotlin 已设计成能与 JVM 库很好互操作，因此在
Kotlin 中使用它们会很自然。然而请注意，`java.util.Scanner` 极其慢。事实上，速度慢得以至用它解析
10<sup>5</sup> 个或更多整数时，很可能不满足典型的 2 秒限制，而这是一个简单的 Kotlin
`split(" ").map { it.toInt() }` 就能做到的。

在 Kotlin 中写输出通常很简单，调用 
[println(...)](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.io/println.html) 
以及使用 Kotlin 的<!-- 
-->[字符串模板](basic-types.md#字符串模板)。然而，当输出<!-- 
-->包含大约 10<sup>5</sup> 或更多行时，必须小心。调用这么多次 `println` 太慢了，因为 
Kotlin 中的该输出会在每行后自动刷新写缓冲。 
从数组或 list 中写多行的更快方式是使用
[joinToString()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/join-to-string.html) 函数<!--
-->以 `"\n"` 作为分隔符，如下所示：

```kotlin
println(a.joinToString("\n")) // 数组/list 中的每个元素占一行
```

## 学习 Kotlin

Kotlin is easy to learn, especially for those who already know Java.
对于软件开发人员来说，关于 Kotlin 基本语法的简短介绍可以直接在<!--
-->以[基本语法](basic-syntax.md)开始的网站参考部分中直接找到。

IDEA 已内置 
[Java-to-Kotlin 转换器](https://www.jetbrains.com/help/idea/converting-a-java-file-to-kotlin-file.html)。 
熟悉 Java 的人可以用它来学习相应的 Kotlin 语法结构，但它<!--
-->并不完美，并且你仍然需要自己熟悉 Kotlin 并学习 
[Kotlin 惯用法](idioms.md)。

学习 Kotlin 语法以及 Kotlin 标准库 API 的一个很好的资源是
[Kotlin 心印](koans.md)。

