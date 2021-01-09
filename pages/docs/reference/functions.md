---
type: doc
layout: reference
category: "Syntax"
title: "函数：infix、vararg、tailrec"
---

# 函数

## 函数声明

Kotlin 中的函数使用 `fun` 关键字声明：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
fun double(x: Int): Int {
    return 2 * x
}
```
</div>

## 函数用法

调用函数使用传统的方法：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
val result = double(2)
```
</div>


调用成员函数使用点表示法：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
Stream().read() // 创建类 Stream 实例并调用 read()
```
</div>

### 参数

函数参数使用 Pascal 表示法定义，即 *name*: *type*。参数用逗号隔开。
每个参数必须有显式类型：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
fun powerOf(number: Int, exponent: Int): Int { /*……*/ }
```
</div>

You can use a [trailing comma](coding-conventions.html#trailing-commas) when you declare function parameters:

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
fun powerOf(
    number: Int,
    exponent: Int, // trailing comma
) { /*...*/ }
```
</div>

### 默认参数

函数参数可以有默认值，当省略相应的参数时使用默认值。与其他语言相比，这可以减少<!--
-->重载数量：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
fun read(
    b: Array<Byte>, 
    off: Int = 0, 
    len: Int = b.size,
) { /*……*/ }
```
</div>

A default value is defined using the `=` after the type.

覆盖方法总是使用与基类型方法相同的默认参数值。
当覆盖一个带有默认参数值的方法时，必须从签名中省略默认参数值：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
open class A {
    open fun foo(i: Int = 10) { /*……*/ }
}

class B : A() {
    override fun foo(i: Int) { /*……*/ }  // 不能有默认值
}
```
</div>

如果一个默认参数在一个无默认值的参数之前，那么该默认值只能通过使用[具名参数](#具名参数)调用该函数来使用：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
fun foo(
    bar: Int = 0, 
    baz: Int,
) { /*……*/ }

foo(baz = 1) // 使用默认值 bar = 0
```
</div>

如果在默认参数之后的最后一个参数是 [lambda 表达式](lambdas.html#lambda-表达式语法)，那么它<!--
-->既可以作为具名参数在括号内传入，也可以在[括号外](lambdas.html#passing-a-lambda-to-the-last-parameter)传入：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
fun foo(
    bar: Int = 0,
    baz: Int = 1,
    qux: () -> Unit,
) { /*……*/ }

foo(1) { println("hello") }     // 使用默认值 baz = 1
foo(qux = { println("hello") }) // 使用两个默认值 bar = 0 与 baz = 1
foo { println("hello") }        // 使用两个默认值 bar = 0 与 baz = 1
```
</div>

### 具名参数

When calling a function, you can name one or more of its arguments. This may be helpful when a function has a large number of arguments, 
and it's difficult to associate a value with an argument, especially if it's a boolean or `null` value.

When you use named arguments in a function call, you can freely change the order they are listed in, and if you want to 
use their default values you can just leave them out altogether.

Consider the following function `reformat()` that has 4 arguments with default values.

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
fun reformat(
    str: String,
    normalizeCase: Boolean = true,
    upperCaseFirstLetter: Boolean = true,
    divideByCamelHumps: Boolean = false,
    wordSeparator: Char = ' ',
) {
/*……*/
}
```

</div>

When calling this function, you don’t have to name all its arguments:

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
reformat(
    'String!',
    false,
    upperCaseFirstLetter = false,
    divideByCamelHumps = true,
    '_'
)
```

</div>

You can skip all arguments with default values:

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
reformat('This is a long String!')
```

</div>

You can skip some arguments with default values. However, after the first skipped argument, you must name all subsequent arguments:

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
reformat('This is a short String!', upperCaseFirstLetter = false, wordSeparator = '_')
```

</div>

You can pass a [variable number of arguments (`vararg`)](#variable-number-of-arguments-varargs) with names using the 
`spread` operator:

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
fun foo(vararg strings: String) { /*……*/ }

foo(strings = *arrayOf("a", "b", "c"))
```

</div>

> **对于 JVM 平台**：在调用 Java 函数时不能使用具名参数语法，因为 Java 字节码并不<!--
-->总是保留函数参数的名称。
{:.note}

### 返回 Unit 的函数

如果一个函数不返回任何有用的值，它的返回类型是 `Unit`。`Unit` 是一种只有一个值——`Unit` 的类型。这个<!--
-->值不需要显式返回：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
fun printHello(name: String?): Unit {
    if (name != null)
        println("Hello $name")
    else
        println("Hi there!")
    // `return Unit` 或者 `return` 是可选的
}
```
</div>

`Unit` 返回类型声明也是可选的。上面的代码等同于：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
fun printHello(name: String?) { …… }
```
</div>

### 单表达式函数

当函数返回单个表达式时，可以省略花括号并且在 **=** 符号之后指定代码体即可：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
fun double(x: Int): Int = x * 2
```
</div>

当返回值类型可由编译器推断时，显式声明返回类型是[可选](#显式返回类型)的：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
fun double(x: Int) = x * 2
```
</div>

### 显式返回类型

具有块代码体的函数必须始终显式指定返回类型，除非他们旨在返回 `Unit`，[在这种情况下它是可选的](#返回-unit-的函数)。
Kotlin 不推断具有块代码体的函数的返回类型，因为这样的函数在代码体中可能有复杂的控制流，并且返回<!--
-->类型对于读者（有时甚至对于编译器）是不明显的。

{:#可变数量的参数varargs}

### 可变数量的参数（Varargs）

函数的参数（通常是最后一个）可以用 `vararg` 修饰符标记：

<div class="sample" markdown="1" theme="idea" data-highlight-only auto-indent="false">

```kotlin
fun <T> asList(vararg ts: T): List<T> {
    val result = ArrayList<T>()
    for (t in ts) // ts is an Array
        result.add(t)
    return result
}
```
</div>

允许将可变数量的参数传递给函数：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
val list = asList(1, 2, 3)
```
</div>

在函数内部，类型 `T` 的 `vararg` 参数的可见方式是作为 `T` 数组，即上例中的 `ts` 变量具有类型 `Array <out T>`。

只有一个参数可以标注为 `vararg`。如果 `vararg` 参数不是列表中的最后一个参数， 可以使用<!--
-->具名参数语法传递其后的参数的值，或者，如果参数具有函数类型，则通过在括号外部<!--
-->传一个 lambda。

当我们调用 `vararg`-函数时，我们可以一个接一个地传参，例如 `asList(1, 2, 3)`，或者，如果我们已经有一个数组<!--
-->并希望将其内容传给该函数，我们使用**伸展（spread）**操作符（在数组前面加 `*`）：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
val a = arrayOf(1, 2, 3)
val list = asList(-1, 0, *a, 4)
```
</div>

### 中缀表示法

标有 *infix*{: .keyword } 关键字的函数也可以使用中缀表示法（忽略该调用的点与圆括号）调用。中缀函数必须满足以下要求：

* 它们必须是成员函数或[扩展函数](extensions.html)；
* 它们必须只有一个参数；
* 其参数不得[接受可变数量的参数](#可变数量的参数varargs)且不能有[默认值](#默认参数)。

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
infix fun Int.shl(x: Int): Int { …… }

// 用中缀表示法调用该函数
1 shl 2

// 等同于这样
1.shl(2)
```
</div>

> 中缀函数调用的优先级低于算术操作符、类型转换以及 `rangeTo` 操作符。
> 以下表达式是等价的：
> * `1 shl 2 + 3` 等价于 `1 shl (2 + 3)`
> * `0 until n * 2` 等价于 `0 until (n * 2)`
> * `xs union ys as Set<*>` 等价于 `xs union (ys as Set<*>)`
>
> 另一方面，中缀函数调用的优先级高于布尔操作符 `&&` 与 `||`、`is-` 与 `in-` 检测以及其他一些操作符。这些表达式也是等价的：
> * `a && b xor c` 等价于 `a && (b xor c)`
> * `a xor b in c` 等价于 `(a xor b) in c`
> 
> 完整的优先级层次结构请参见其[语法参考](grammar.html#expressions)。
{:.note}

请注意，中缀函数总是要求指定接收者与参数。当<!--
-->使用中缀表示法在当前接收者上调用方法时，需要显式使用 `this`；不能像常规方法调用<!--
-->那样省略。这是确保非模糊解析所必需的。

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
class MyStringCollection {
    infix fun add(s: String) { /*……*/ }
    
    fun build() {
        this add "abc"   // 正确
        add("abc")       // 正确
        //add "abc"        // 错误：必须指定接收者
    }
}
```
</div>


## 函数作用域

在 Kotlin 中函数可以在文件顶层声明，这意味着你不需要像一些语言如 Java、C# 或 Scala 那样需要创建一个类来保存一个函数。此外<!--
-->除了顶层函数，Kotlin 中函数也可以声明在局部作用域、作为成员函数以及扩展函数。

### 局部函数

Kotlin 支持局部函数，即一个函数在另一个函数内部：

<div class="sample" markdown="1" theme="idea" data-highlight-only auto-indent="false">

```kotlin
fun dfs(graph: Graph) {
    fun dfs(current: Vertex, visited: MutableSet<Vertex>) {
        if (!visited.add(current)) return
        for (v in current.neighbors)
            dfs(v, visited)
    }

    dfs(graph.vertices[0], HashSet())
}
```
</div>

局部函数可以访问外部函数（即闭包）的局部变量，所以在上例中，*visited* 可以是局部变量：

<div class="sample" markdown="1" theme="idea" data-highlight-only auto-indent="false">

```kotlin
fun dfs(graph: Graph) {
    val visited = HashSet<Vertex>()
    fun dfs(current: Vertex) {
        if (!visited.add(current)) return
        for (v in current.neighbors)
            dfs(v)
    }

    dfs(graph.vertices[0])
}
```
</div>

### 成员函数

成员函数是在类或对象内部定义的函数：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
class Sample {
    fun foo() { print("Foo") }
}
```
</div>

成员函数以点表示法调用：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
Sample().foo() // 创建类 Sample 实例并调用 foo
```
</div>

关于类和覆盖成员的更多信息参见[类](classes.html)和[继承](classes.html#继承)。

## 泛型函数

函数可以有泛型参数，通过在函数名前使用尖括号指定：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
fun <T> singletonList(item: T): List<T> { /*……*/ }
```
</div>

关于泛型函数的更多信息参见[泛型](generics.html)。

## 内联函数

内联函数在[这里](inline-functions.html)讲述。

## 扩展函数

扩展函数在[其自有章节](extensions.html)讲述。

## 高阶函数和 Lambda 表达式

高阶函数和 Lambda 表达式在[其自有章节](lambdas.html)讲述。

## 尾递归函数

Kotlin 支持一种称为[尾递归](https://zh.wikipedia.org/wiki/%E5%B0%BE%E8%B0%83%E7%94%A8)的函数式编程风格。
这允许一些通常用循环写的算法改用递归函数来写，而无堆栈溢出的风险。
当一个函数用 `tailrec` 修饰符标记并满足所需的形式时，编译器会优化该递归，留下一个快速而高效的基于循环的版本：

<div class="sample" markdown="1" theme="idea" data-highlight-only auto-indent="false">

```kotlin
val eps = 1E-10 // "good enough", could be 10^-15

tailrec fun findFixPoint(x: Double = 1.0): Double =
    if (Math.abs(x - Math.cos(x)) < eps) x else findFixPoint(Math.cos(x))
```
</div>

这段代码计算余弦的不动点（fixpoint of cosine），这是一个数学常数。 它只是重复地从 1.0 开始调用 Math.cos，直到结果不再改变，对于这里指定的 `eps` 精度会产生 0.7390851332151611 的结果。最终代码相当于这种更传统风格的代码：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
val eps = 1E-10 // "good enough", could be 10^-15

private fun findFixPoint(): Double {
    var x = 1.0
    while (true) {
        val y = Math.cos(x)
        if (Math.abs(x - y) < eps) return x
        x = Math.cos(x)
    }
}
```
</div>

要符合 `tailrec` 修饰符的条件的话，函数必须将其自身调用作为它执行的最后一个操作。在递归调用后有更多代码时，不能使用尾递归，并且不能用在 try/catch/finally 块中。目前在 Kotlin for JVM 与 Kotlin/Native 中支持尾递归。
