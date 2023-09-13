[//]: # (title: 函数)

Kotlin 函数使用 `fun` 关键字声明：

```kotlin
fun double(x: Int): Int {
    return 2 * x
}
```

## 函数用法

使用常规方式来调用函数：

```kotlin
val result = double(2)
```

调用成员函数使用点表示法：

```kotlin
Stream().read() // 创建类 Stream 实例并调用 read()
```

### 参数

函数参数使用 Pascal 表示法定义——*name*: *type*。参数用逗号隔开，
每个参数必须有显式类型：

```kotlin
fun powerOf(number: Int, exponent: Int): Int { /*……*/ }
```

You can use a [trailing comma](coding-conventions.md#trailing-commas) when you declare function parameters:

```kotlin
fun powerOf(
    number: Int,
    exponent: Int, // trailing comma
) { /*...*/ }
```

### 默认参数

函数参数可以有默认值，当省略相应的参数时使用默认值。这可以减少<!--
-->重载数量：

```kotlin
fun read(
    b: ByteArray,
    off: Int = 0,
    len: Int = b.size,
) { /*……*/ }
```

通过在类型的后面添加 `=` 符号来设置参数的默认值。

覆盖方法总是使用与基类型方法相同的默认参数值。
当覆盖一个有默认参数值的方法时，必须从签名中省略默认参数值：

```kotlin
open class A {
    open fun foo(i: Int = 10) { /*……*/ }
}

class B : A() {
    override fun foo(i: Int) { /*……*/ }  // 不能有默认值
}
```

如果一个有默认值参数在一个无默认值的参数之前，那么该默认值只能通过<!--
-->使用[具名参数](#具名参数)调用该函数来使用：

```kotlin
fun foo(
    bar: Int = 0,
    baz: Int,
) { /*……*/ }

foo(baz = 1) // 使用默认值 bar = 0
```

如果在默认参数之后的最后一个参数是 [lambda 表达式](lambdas.md#lambda-表达式语法)，那么它<!--
-->既可以作为具名参数在括号内传入，也可以在[括号外](lambdas.md#传递末尾的-lambda-表达式)传入：

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

### 具名参数

在调用函数的时候可以指明一个或多个参数，这对于包含多个参数的函数是十分有帮助的，尤其是在传入 `null` 或者布尔值的情况下很难联想到参数的含义。 

在调用函数的过程中使用具名参数时，你可以随意修改参数的顺序。如果想要使用某些参数的默认值，只需要在传参的时候省略掉这些参数即可。

 以 `reformat()` 函数为例，该函数包含 4 个有默认值的参数。

```kotlin
fun reformat(
    str: String,
    normalizeCase: Boolean = true,
    upperCaseFirstLetter: Boolean = true,
    divideByCamelHumps: Boolean = false,
    wordSeparator: Char = ' ',
) { /*……*/ }
```

当调用这个函数时，你也可以不指明所有参数的名称：

```kotlin
reformat(
    "String!",
    false,
    upperCaseFirstLetter = false,
    divideByCamelHumps = true,
    '_'
)
```

你也可以省略所有有默认值的参数：

```kotlin
reformat("This is a long String!")
```

除了省略掉所有有默认值的参数，你也可以选择只省略某些特定的有默认值的参数。但是你需要在跳过第一个参数后，对后续的所有参数都使用具名参数：

```kotlin
reformat("This is a short String!", upperCaseFirstLetter = false, wordSeparator = '_')
```

可以通过具名参数和 `展开运算符` 来传入[可变参数（`vararg`）](#可变数量的参数varargs)
```kotlin
fun foo(vararg strings: String) { /*……*/ }

foo(strings = *arrayOf("a", "b", "c"))
```

> 在 JVM 平台中调用 Java 函数时不能使用具名参数语法，因为 Java 字节码并不<!--
> -->总是保留函数参数的名称。
>
{type="note"}

### 返回 Unit 的函数

如果一个函数并不返回有用的值，其返回类型是 `Unit`。`Unit` 是一种只有一个值——`Unit` 的类型。
这个值不需要显式返回：

```kotlin
fun printHello(name: String?): Unit {
    if (name != null)
        println("Hello $name")
    else
        println("Hi there!")
    // `return Unit` 或者 `return` 是可选的
}
```

`Unit` 返回类型声明也是可选的。上面的代码等同于：

```kotlin
fun printHello(name: String?) { …… }
```

### 单表达式函数

当函数返回单个表达式时，可以省略花括号并且在 `=` 符号之后指定代码体即可：

```kotlin
fun double(x: Int): Int = x * 2
```

当返回值类型可由编译器推断时，显式声明返回类型是[可选](#显式返回类型)的：

```kotlin
fun double(x: Int) = x * 2
```

### 显式返回类型

具有块代码体的函数必须始终显式指定返回类型，除非他们旨在返回 `Unit`，
[在这种情况下指定返回值类型是可选的](#返回-unit-的函数)。

Kotlin 不推断具有块代码体的函数的返回类型，因为这样的函数在代码体中可能有复杂的控制流，
并且返回类型对于读者（有时甚至对于编译器）是不明显的。

### 可变数量的参数（varargs）

函数的参数（通常是最后一个）可以用 `vararg` 修饰符标记：

```kotlin
fun <T> asList(vararg ts: T): List<T> {
    val result = ArrayList<T>()
    for (t in ts) // ts is an Array
        result.add(t)
    return result
}
```

在本例中，可以将可变数量的参数传递给函数：

```kotlin
val list = asList(1, 2, 3)
```

在函数内部，类型 `T` 的 `vararg` 参数的可见方式是作为 `T` 数组，如上例中的 `ts`
变量具有类型 `Array <out T>`。

只有一个参数可以标注为 `vararg`。如果 `vararg` 参数不是列表中的最后一个参数， 可以使用<!--
-->具名参数语法传递其后的参数的值，或者，如果参数具有函数类型，则通过在括号外部<!--
-->传一个 lambda。

当调用 `vararg`-函数时，可以逐个传参，例如 `asList(1, 2, 3)`。如果已经有一个数组<!--
-->并希望将其内容传给该函数，那么使用*展开*操作符（在数组前面加 `*`）：

```kotlin
val a = arrayOf(1, 2, 3)
val list = asList(-1, 0, *a, 4)
```

If you want to pass a [primitive type array](arrays.md#原生类型数组)
into `vararg`, you need to convert it to a regular (typed) array using the `toTypedArray()` function:

```kotlin
val a = intArrayOf(1, 2, 3) // IntArray is a primitive type array
val list = asList(-1, 0, *a.toTypedArray(), 4)
```

### 中缀表示法

标有 `infix` 关键字的函数也可以使用中缀表示法（忽略该调用的点与圆括号）调用。
中缀函数必须满足以下要求：

* 它们必须是成员函数或[扩展函数](extensions.md)。
* 它们必须只有一个参数。
* 其参数不得[接受可变数量的参数](#可变数量的参数varargs)且<!--
-->不能有[默认值](#默认参数)。

```kotlin
infix fun Int.shl(x: Int): Int { …… }

// 用中缀表示法调用该函数
1 shl 2

// 等同于这样
1.shl(2)
```

> 中缀函数调用的优先级低于算术操作符、类型转换以及 `rangeTo` 操作符。
> 以下表达式是等价的：
> * `1 shl 2 + 3` 等价于 `1 shl (2 + 3)`
> * `0 until n * 2` 等价于 `0 until (n * 2)`
> * `xs union ys as Set<*>` 等价于 `xs union (ys as Set<*>)`
>
> 另一方面，中缀函数调用的优先级高于布尔操作符 `&&` 与 `||`、`is-`
> 与 `in-` 检测以及其他一些操作符。这些表达式也是等价的：
> * `a && b xor c` 等价于 `a && (b xor c)`
> * `a xor b in c` 等价于 `(a xor b) in c`
>
{type="note"}

请注意，中缀函数总是要求指定接收者与参数。当<!--
-->使用中缀表示法在当前接收者上调用方法时，需要显式使用 `this`。这是确保<!--
-->非模糊解析所必需的。

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

## 函数作用域

Kotlin 函数可以在文件顶层声明，这意味着你不需要像一些语言如
Java、C# 与 Scala ([top level definition is available since Scala 3](https://docs.scala-lang.org/scala3/book/taste-toplevel-definitions.html#inner-main)) 那样需要创建一个类来保存一个函数。此外<!--
-->除了顶层函数，Kotlin 中函数也可以声明在局部作用域、作为成员函数以及扩展函数。

### 局部函数

Kotlin 支持局部函数，即一个函数在另一个函数内部：

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

局部函数可以访问外部函数（闭包）的局部变量。在上例中，`visited` 可以是局部变量：

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

### 成员函数

成员函数是在类或对象内部定义的函数：

```kotlin
class Sample {
    fun foo() { print("Foo") }
}
```

成员函数以点表示法调用：

```kotlin
Sample().foo() // 创建类 Sample 实例并调用 foo
```

关于类和覆盖成员的更多信息参见[类](classes.md)和[继承](classes.md#继承)。

## 泛型函数

函数可以有泛型参数，通过在函数名前使用尖括号指定：

```kotlin
fun <T> singletonList(item: T): List<T> { /*……*/ }
```

关于泛型函数的更多信息，请参见[泛型](generics.md)。

## 尾递归函数

Kotlin 支持一种称为[尾递归](https://zh.wikipedia.org/wiki/%E5%B0%BE%E8%B0%83%E7%94%A8)的函数式编程风格。
对于某些使用循环的算法，可以使用尾递归替代而不会有堆栈溢出的风险。
当一个函数用 `tailrec` 修饰符标记并满足所需的形式条件时，编译器会优化该递归，
留下一个快速而高效的基于循环的版本：

```kotlin
val eps = 1E-10 // "good enough", could be 10^-15

tailrec fun findFixPoint(x: Double = 1.0): Double =
    if (Math.abs(x - Math.cos(x)) < eps) x else findFixPoint(Math.cos(x))
```

这段代码计算余弦的不动点（`fixpoint` of cosine），这是一个数学常数。 它只是重复地从 `1.0` 开始调用 `Math.cos`，
直到结果不再改变，对于这里指定的
`eps` 精度会产生 `0.7390851332151611` 的结果。最终代码相当于这种更传统风格的代码：

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

要符合 `tailrec` 修饰符的条件的话，函数必须将其自身调用作为它执行的最后一个操作。在递归调用后有更多代码时，
不能使用尾递归，不能用在 `try`/`catch`/`finally` 块中，也不能用于 open 的函数。
目前在 Kotlin for the JVM 与 Kotlin/Native 中支持尾递归。

**See also**:
* [内联函数](inline-functions.md)
* [扩展函数](extensions.md)
* [高阶函数与 Lambda 表达式](lambdas.md)

