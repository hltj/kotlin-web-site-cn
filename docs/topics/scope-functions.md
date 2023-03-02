[//]: # (title: 作用域函数)

Kotlin 标准库包含几个函数，它们的唯一目的是在对象的上下文中执行代码块。
当对一个对象调用这样的函数并提供一个 [lambda 表达式](lambdas.md)时，它会形成一个<!--
-->临时作用域。在此作用域中，可以访问该对象而无需其名称。这些函数称为*作用域函数*。
共有以下五种：[`let`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/let.html)、 [`run`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/run.html)、
[`with`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/with.html)、 [`apply`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/apply.html)、
以及 [`also`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/also.html)。

这些函数基本上都执行同样的操作：在一个对象上执行一个代码块。不同的是这个对象在块中<!--
-->如何使用，以及整个表达式的结果是什么。

下面是作用域函数用法的典型示例:

```kotlin
data class Person(var name: String, var age: Int, var city: String) {
    fun moveTo(newCity: String) { city = newCity }
    fun incrementAge() { age++ }
}

fun main() {
//sampleStart
    Person("Alice", 20, "Amsterdam").let {
        println(it)
        it.moveTo("London")
        it.incrementAge()
        println(it)
    }
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

如果不使用 `let` 来写这段代码，就必须引入一个新变量，并在每次使用它时重复其名称。

```kotlin
data class Person(var name: String, var age: Int, var city: String) {
    fun moveTo(newCity: String) { city = newCity }
    fun incrementAge() { age++ }
}

fun main() {
//sampleStart
    val alice = Person("Alice", 20, "Amsterdam")
    println(alice)
    alice.moveTo("London")
    alice.incrementAge()
    println(alice)
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

作用域函数没有引入任何新的技术，但是它们可以使你的代码更加简洁易读。

由于作用域函数的许多相似性，因此为你的案例选择正确的函数可能有点棘手。
选择主要取决于你的意图与在项目中使用的一致性。下面我们将详细描述<!--
-->各种作用域函数的差异及其约定用法。

## 函数选择

为了帮助你选择合适的作用域函数，我们提供了总结它们之间的主要差异的<!--
-->这张表。

| 函数|对象引用|返回值|是否是扩展函数|
|---|---|---|---|
| [`let`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/let.html) |`it`|Lambda 表达式结果|是|
| [`run`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/run.html) |`this`|Lambda 表达式结果|是|
| [`run`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/run.html) |-|Lambda 表达式结果|不是：调用无需上下文对象|
| [`with`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/with.html) |`this`|Lambda 表达式结果|不是：把上下文对象当做参数|
| [`apply`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/apply.html) |`this`|上下文对象|是|
| [`also`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/also.html) |`it`|上下文对象|是|

Detailed information about these functions is provided in the dedicated sections below.

以下是根据预期目的选择作用域函数的简短指南：

* 对一个非空（non-null）对象执行 lambda 表达式：`let`
* 将表达式作为变量引入为局部作用域中：`let`
* 对象配置：`apply`
* 对象配置并且计算结果：`run`
* 在需要表达式的地方运行语句：非扩展的 `run`
* 附加效果：`also`
* 一个对象的一组函数调用：`with`

不同作用域函数的使用场景存在重叠，可以根据项目或团队中使用的特定<!--
-->约定来选择使用哪些函数。

虽然作用域函数可以让代码更加简洁，但是要避免过度使用它们：这会使代码难以阅读并<!--
-->可能导致错误。 我们还建议避免嵌套作用域函数，同时链式调用它们时要小心：因为很容易<!--
-->混淆当前上下文对象与 `this` 或 `it` 的值。

## 区别

由于作用域函数本质上都非常相似，因此了解它们之间的区别很重要。
每个作用域函数之间有两个主要区别：
* 引用上下文对象的方式。
* 返回值。

### 上下文对象：this 还是 it

在传入作用域函数的 lambda 表达式内部，上下文对象可以不使用其实际名称而是使用一个更简短的引用来访问。
每个作用域函数都使用以下两种方式之一来引用上下文对象：作为 lambda 表达式的[接收者](lambdas.md#带有接收者的函数字面值)
（`this`）或者作为 lambda 表达式的参数（`it`）。两者都提供了同样的功能，因此我们会针对不同的场景<!--
-->描述两者的优缺点，并提供使用建议。

```kotlin
fun main() {
    val str = "Hello"
    // this
    str.run {
        println("The string's length: $length")
        //println("The string's length: ${this.length}") // 和上句效果相同
    }

    // it
    str.let {
        println("The string's length is ${it.length}")
    }
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

#### this

`run`、`with` 以及 `apply` 通过关键字 `this` 将上下文对象引用为 lambda 表达式的[接收者](lambdas.md#带有接收者的函数字面值)。
因此，在它们的 lambda 表达式中可以像在普通的类函数中一样访问上下文对象。

在大多数场景，当你访问接收者对象时你可以省略 `this`， 来让你的代码更简短。
相对地，如果省略了 `this`，就很难区分接收者对象的成员及外部对象或<!--
-->函数。因此，对于主要对对象的成员进行操作（调用其函数或赋值其属性）的 lambda 表达式，
建议将上下文对象作为接收者（`this`）。

```kotlin
data class Person(var name: String, var age: Int = 0, var city: String = "")

fun main() {
//sampleStart
    val adam = Person("Adam").apply { 
        age = 20                       // 同 this.age = 20
        city = "London"
    }
    println(adam)
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

#### it

反过来，`let` 及 `also` 将上下文对象引用为 lambda 表达式[参数](lambdas.md#lambda-表达式语法)。如果<!--
-->没有指定参数名，对象可以用隐式默认名称 `it` 访问。`it` 比
`this` 简短，带有 `it` 的表达式通常更易读。

不过，当调用对象函数或属性时，不能像 `this` 这样隐式地访问对象。
因此，当上下文对象在作用域中主要用作函数调用中的参数时，通过 `it` 访问上下文对象会更好。
在代码块中使用多个变量时，`it` 也更好一些。

```kotlin
import kotlin.random.Random

fun writeToLog(message: String) {
    println("INFO: $message")
}

fun main() {
//sampleStart
    fun getRandomInt(): Int {
        return Random.nextInt(100).also {
            writeToLog("getRandomInt() generated value $it")
        }
    }
    
    val i = getRandomInt()
    println(i)
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

The example below demonstrates referencing the context object as a lambda argument with argument name: `value`.

```kotlin
import kotlin.random.Random

fun writeToLog(message: String) {
    println("INFO: $message")
}

fun main() {
//sampleStart
    fun getRandomInt(): Int {
        return Random.nextInt(100).also { value ->
            writeToLog("getRandomInt() generated value $value")
        }
    }
    
    val i = getRandomInt()
    println(i)
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

### 返回值

根据返回结果，作用域函数可以分为以下两类：
* `apply` 及 `also` 返回上下文对象。
* `let`、`run` 及 `with` 返回 lambda 表达式结果.

You should consider carefully what return value you want based on what you want to do next in your code. This helps you
to choose the best scope function to use.

#### 上下文对象

`apply` 及 `also` 的返回值是上下文对象本身。因此，它们可以作为<!--
-->*辅助步骤*包含在调用链中：可以继续在同一个对象上一个接一个地进行链式函数调用。

```kotlin
fun main() {
//sampleStart
    val numberList = mutableListOf<Double>()
    numberList.also { println("Populating the list") }
        .apply {
            add(2.71)
            add(3.14)
            add(1.0)
        }
        .also { println("Sorting the list") }
        .sort()
//sampleEnd
    println(numberList)
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

它们还可以用在返回上下文对象的函数的 return 语句中。

```kotlin
import kotlin.random.Random

fun writeToLog(message: String) {
    println("INFO: $message")
}

fun main() {
//sampleStart
    fun getRandomInt(): Int {
        return Random.nextInt(100).also {
            writeToLog("getRandomInt() generated value $it")
        }
    }
    
    val i = getRandomInt()
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

#### Lambda 表达式结果

`let`、`run` 及 `with` 返回 lambda 表达式的结果。所以，在需要使用其结果给一个变量赋值，或者在需要对其结果进行链式<!--
-->操作等情况下，可以使用它们。

```kotlin
fun main() {
//sampleStart
    val numbers = mutableListOf("one", "two", "three")
    val countEndsWithE = numbers.run { 
        add("four")
        add("five")
        count { it.endsWith("e") }
    }
    println("There are $countEndsWithE elements that end with e.")
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

此外，还可以忽略返回值，仅使用作用域函数为局部变量创建一个临时作用域。

```kotlin
fun main() {
//sampleStart
    val numbers = mutableListOf("one", "two", "three")
    with(numbers) {
        val firstItem = first()
        val lastItem = last()        
        println("First item: $firstItem, last item: $lastItem")
    }
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

## 几个函数

为了帮助你为应用场景选择合适的作用域函数，我们会详细地描述它们并且提供<!--
-->一些使用建议。 从技术角度讲，作用域函数在很多场景里是可以互换的，所以这些示例<!--
-->展示了使用它们的约定。

### let

- **上下文对象**作为 lambda 表达式的参数（`it`）来访问。
- **返回值**是 lambda 表达式的结果。

[`let`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/let.html) 可用于在调用链的结果上<!--
-->调用一个或多个函数。例如，以下代码打印对集合的两个操作的结果：

```kotlin
fun main() {
//sampleStart
    val numbers = mutableListOf("one", "two", "three", "four", "five")
    val resultList = numbers.map { it.length }.filter { it > 3 }
    println(resultList)
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

With `let`, you can rewrite the above example so that you're not assigning the result of the list
operations to a variable:

```kotlin
fun main() {
//sampleStart
    val numbers = mutableListOf("one", "two", "three", "four", "five")
    numbers.map { it.length }.filter { it > 3 }.let { 
        println(it)
        // 如果需要可以调用更多函数
    } 
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

如果传给 `let` 的代码块仅包含以 `it` 作为参数的单个函数，那么可以使用方法引用<!--
-->（`::`）代替 lambda 表达式参数：

```kotlin
fun main() {
//sampleStart
    val numbers = mutableListOf("one", "two", "three", "four", "five")
    numbers.map { it.length }.filter { it > 3 }.let(::println)
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

`let` 经常用于执行包含非空值代码块。如需对非空对象执行操作，
可对其使用[安全调用操作符 `?.`](null-safety.md#安全的调用) 并调用 `let` 在 lambda 表达式中执行操作。

```kotlin
fun processNonNullString(str: String) {}

fun main() {
//sampleStart
    val str: String? = "Hello" 
    //processNonNullString(str)       // 编译错误：str 可能为空
    val length = str?.let { 
        println("let() called on $it")
        processNonNullString(it)      // 编译通过：'it' 在 '?.let { }' 中必不为空
        it.length
    }
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

也可以使用 `let` 引入作用域受限的局部变量使代码更易读。
如需为上下文对象定义一个新变量，可提供其名称作为 lambda 表达式参数来代替<!--
-->默认的 `it`。

```kotlin
fun main() {
//sampleStart
    val numbers = listOf("one", "two", "three", "four")
    val modifiedFirstItem = numbers.first().let { firstItem ->
        println("The first item of the list is '$firstItem'")
        if (firstItem.length >= 5) firstItem else "!" + firstItem + "!"
    }.uppercase()
    println("First item after modifications: '$modifiedFirstItem'")
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

### with

- **上下文对象**作为接收者（`this`）使用。
- **返回值**是 lambda 表达式结果。

As [`with`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/with.html) is not an extension function: the context
object is passed as an argument, but inside the lambda, it's available as a receiver (`this`).

我们建议当不需要使用 lambda 表达式结果时，使用 `with` 来调用上下文对象上的函数。
在代码中，`with` 可以理解为“*对于这个对象，执行以下操作。*”

```kotlin
fun main() {
//sampleStart
    val numbers = mutableListOf("one", "two", "three")
    with(numbers) {
        println("'with' is called with argument $this")
        println("It contains $size elements")
    }
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

还可以用 `with` 来引入一个辅助对象，其属性或函数用于计算一个值。

```kotlin
fun main() {
//sampleStart
    val numbers = mutableListOf("one", "two", "three")
    val firstAndLast = with(numbers) {
        "The first element is ${first()}," +
        " the last element is ${last()}"
    }
    println(firstAndLast)
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

### run

- **上下文对象** 作为接收者（`this`）来访问。
- **返回值** 是 lambda 表达式结果。

[`run`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/run.html) does the same as `with` but it is implemented as 
an extension function. So like `let`, you can call it on the context object using dot notation.

当 lambda 表达式同时初始化对象并计算返回值时，`run` 很有用。

```kotlin
class MultiportService(var url: String, var port: Int) {
    fun prepareRequest(): String = "Default request"
    fun query(request: String): String = "Result for query '$request'"
}

fun main() {
//sampleStart
    val service = MultiportService("https://example.kotlinlang.org", 80)

    val result = service.run {
        port = 8080
        query(prepareRequest() + " to port $port")
    }
    
    // 同样的代码如果用 let() 函数来写:
    val letResult = service.let {
        it.port = 8080
        it.query(it.prepareRequest() + " to port ${it.port}")
    }
//sampleEnd
    println(result)
    println(letResult)
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

You can also invoke `run` as a non-extension function. The non-extension variant of `run` has no context object, but it
still returns the lambda result. 非扩展 `run` 可以在需要表达式的地方执行一个由多个语句组成的<!--
-->块。

```kotlin
fun main() {
//sampleStart
    val hexNumberRegex = run {
        val digits = "0-9"
        val hexDigits = "A-Fa-f"
        val sign = "+-"
        
        Regex("[$sign]?[$digits$hexDigits]+")
    }
    
    for (match in hexNumberRegex.findAll("+123 -FFFF !%*& 88 XYZ")) {
        println(match.value)
    }
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

### apply

- **上下文对象** 作为接收者（`this`）来访问。
- **返回值** 是上下文对象本身。

As [`apply`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/apply.html) returns the context object itself, we 
recommend that 对于不返回值且主要在接收者（`this`）对象的成员上运行的代码块<!--
-->使用它。`apply`最常见的使用场景是用于对象配置。这样的调用可以理解为“*将<!---
->以下赋值操作应用于对象*”。

```kotlin
data class Person(var name: String, var age: Int = 0, var city: String = "")

fun main() {
//sampleStart
    val adam = Person("Adam").apply {
        age = 32
        city = "London"        
    }
    println(adam)
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

Another use case for `apply` is to include `apply` in multiple call chains for more complex processing.

### also

- **上下文对象**作为 lambda 表达式的参数（`it`）来访问。
- **返回值**是上下文对象本身。

[`also`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/also.html) 对于执行一些将<!--
-->上下文对象作为参数的操作很有用。 对于需要引用对象而不是其属性与<!--
-->函数的操作，或者不想屏蔽来自外部作用域的 `this` 引用时，请使用 `also`。

当你在代码中看到 `also` 时，可以将其理解为“*并且用该对象执行以下操作*”。

```kotlin
fun main() {
//sampleStart
    val numbers = mutableListOf("one", "two", "three")
    numbers
        .also { println("The list elements before adding new one: $it") }
        .add("four")
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

## takeIf 与 takeUnless

除了作用域函数外，标准库还包含函数 [`takeIf`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/take-if.html)
及 [`takeUnless`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/take-unless.html)。 这俩函数让你可以将<!--
-->对象状态检查嵌入到调用链中。

当在对象上调用谓词时，若满足给定的谓词，则 `takeIf` 返回此对象。
否则返回 `null`。因此，`takeIf` 是单个对象的过滤函数。

`takeUnless` has the opposite logic of `takeIf`. When called on an object along with a predicate, `takeUnless` returns 
`null` if it satisfies the given predicate. Otherwise, it returns the object.

When using `takeIf` or `takeUnless`, the object is available as a lambda argument (`it`).

```kotlin
import kotlin.random.*

fun main() {
//sampleStart
    val number = Random.nextInt(100)

    val evenOrNull = number.takeIf { it % 2 == 0 }
    val oddOrNull = number.takeUnless { it % 2 == 0 }
    println("even: $evenOrNull, odd: $oddOrNull")
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

> 当在 `takeIf` 及 `takeUnless` 之后链式调用其他函数，不要忘记执行空检查或使用安全调用
> （`?.`），因为他们的返回值是可为空的。

```kotlin
fun main() {
//sampleStart
    val str = "Hello"
    val caps = str.takeIf { it.isNotEmpty() }?.uppercase()
   //val caps = str.takeIf { it.isNotEmpty() }.uppercase() // 编译错误
    println(caps)
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

`takeIf` 及 `takeUnless` 与作用域函数结合使用时特别有用。 例如，可以将
`takeIf` 及 `takeUnless` 与 `let` 链式调用，以便在与给定谓词匹配的对象上运行代码块。为此，
请在对象上调用 `takeIf`，然后通过安全调用（`?.`）调用 `let`。对于与谓词不匹配的对象，
`takeIf` 返回 `null`，并且不调用 `let`。

```kotlin
fun main() {
    //sampleStart
    fun displaySubstringPosition(input: String, sub: String) {
        input.indexOf(sub).takeIf { it >= 0 }?.let {
            println("The substring $sub is found in $input.")
            println("Its start position is $it.")
        }
    }

    displaySubstringPosition("010000011", "11")
    displaySubstringPosition("010000011", "12")
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

For comparison, below is an example of how the same function can be written without using `takeIf` or scope functions:

```kotlin
fun main() {
    //sampleStart
    fun displaySubstringPosition(input: String, sub: String) {
        val index = input.indexOf(sub)
        if (index >= 0) {
            println("The substring $sub is found in $input.")
            println("Its start position is $index.")
        }
    }

    displaySubstringPosition("010000011", "11")
    displaySubstringPosition("010000011", "12")
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

