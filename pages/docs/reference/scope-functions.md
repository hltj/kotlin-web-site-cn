---
type: doc
layout: reference
category: "Syntax"
title: "作用域函数"
---

# 作用域函数

Kotlin 标准库包含几个函数，它们的唯一目的是在对象的上下文中执行代码块。当对一个对象调用这样的函数并提供一个 [lambda 表达式](lambdas.html)时，它会形成一个临时作用域。在此作用域中，可以访问该对象而无需其名称。这些函数称为*作用域函数*。共有以下五种：`let`、`run`、`with`、`apply` 以及 `also`。

这些函数基本上做了同样的事情：在一个对象上执行一个代码块。不同的是这个对象在块中如何使用，以及整个表达式的结果是什么。

下面是作用域函数的典型用法:



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



作用域函数没有引入任何新的技术，但是它们可以使你的代码更加简洁易读。

由于作用域函数的相似性质，为你的案例选择正确的函数可能有点棘手。选择主要取决于你的意图和项目中使用的一致性。下面我们将详细描述各种作用域函数及其约定用法之间的区别。

## 区别

由于作用域函数本质上都非常相似，因此了解它们之间的区别很重要。每个作用域函数之间有两个主要区别：
* 引用上下文对象的方式
* 返回值

### 上下文对象：`this` 还是 `it`

在作用域函数的 lambda 表达式里，上下文对象可以不使用其实际名称而是使用一个更简短的引用来访问。每个作用域函数都使用以下两种方式之一来访问上下文对象：作为 lambda 表达式的[接收者](lambdas.html#带有接收者的函数字面值)（`this`）或者作为 lambda 表达式的参数（`it`）。两者都提供了同样的功能，因此我们将针对不同的场景描述两者的优缺点，并提供使用建议。



```kotlin
fun main() {
    val str = "Hello"
    // this
    str.run {
        println("The receiver string length: $length")
        //println("The receiver string length: ${this.length}") // 和上句效果相同
    }

    // it
    str.let {
        println("The receiver string's length is ${it.length}")
    }
}
```



#### this

`run`、`with` 以及 `apply` 通过关键字 `this` 引用上下文对象。因此，在它们的 lambda 表达式中可以像在普通的类函数中一样访问上下文对象。在大多数场景，当你访问接收者对象时你可以省略 `this`，来让你的代码更简短。相对地，如果省略了 `this`，就很难区分接收者对象的成员及外部对象或函数。因此，对于主要对对象成员进行操作（调用其函数或赋值其属性）的 lambda 表达式，建议将上下文对象作为接收者（`this`）。



```kotlin
data class Person(var name: String, var age: Int = 0, var city: String = "")

fun main() {
//sampleStart
    val adam = Person("Adam").apply { 
        age = 20                       // 和 this.age = 20 或者 adam.age = 20 一样
        city = "London"
    }
    println(adam)
//sampleEnd
}
```



#### it

反过来，`let` 及 `also` 将上下文对象作为 lambda 表达式参数。如果没有指定参数名，对象可以用隐式默认名称 `it` 访问。`it` 比 `this` 简短，带有 `it` 的表达式通常更容易阅读。然而，当调用对象函数或属性时，不能像 `this` 这样隐式地访问对象。因此，当上下文对象在作用域中主要用作函数调用中的参数时，使用 `it` 作为上下文对象会更好。若在代码块中使用多个变量，则 `it` 也更好。



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



此外，当将上下文对象作为参数传递时，可以为上下文对象指定在作用域内的自定义名称。



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
//sampleEnd
}
```



### 返回值

根据返回结果，作用域函数可以分为以下两类：
* `apply` 及 `also` 返回上下文对象。
* `let`、`run` 及 `with` 返回 lambda 表达式结果.

这两个选项使你可以根据在代码中的后续操作来选择适当的函数。

#### 上下文对象

`apply` 及 `also` 的返回值是上下文对象本身。因此，它们可以作为辅助步骤包含在调用链中：你可以继续在同一个对象上进行链式函数调用。



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



#### Lambda 表达式结果

`let`、`run` 及 `with` 返回 lambda 表达式的结果。所以，在需要使用其结果给一个变量赋值，或者在需要对其结果进行链式操作等情况下，可以使用它们。



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



此外，还可以忽略返回值，仅使用作用域函数为变量创建一个临时作用域。



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



## 几个函数

为了帮助你为你的场景选择合适的作用域函数，我们会详细地描述它们并且提供一些使用建议。从技术角度来说，作用域函数在很多场景里是可以互换的，所以这些示例展示了定义通用使用风格的约定用法。

### `let`

**上下文对象**作为 lambda 表达式的参数（`it`）来访问。**返回值**是 lambda 表达式的结果。

`let` 可用于在调用链的结果上调用一个或多个函数。例如，以下代码打印对集合的两个操作的结果：



```kotlin
fun main() {
//sampleStart
    val numbers = mutableListOf("one", "two", "three", "four", "five")
    val resultList = numbers.map { it.length }.filter { it > 3 }
    println(resultList)
//sampleEnd
}
```



使用 `let`，可以写成这样：



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



若代码块仅包含以 `it` 作为参数的单个函数，则可以使用方法引用(`::`)代替 lambda 表达式：



```kotlin
fun main() {
//sampleStart
    val numbers = mutableListOf("one", "two", "three", "four", "five")
    numbers.map { it.length }.filter { it > 3 }.let(::println)
//sampleEnd
}
```



`let` 经常用于仅使用非空值执行代码块。如需对非空对象执行操作，可对其使用安全调用操作符 `?.` 并调用 `let` 在 lambda 表达式中执行操作。



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



使用 `let` 的另一种情况是引入作用域受限的局部变量以提高代码的可读性。如需为上下文对象定义一个新变量，可提供其名称作为 lambda 表达式参数来替默认的 `it`。



```kotlin
fun main() {
//sampleStart
    val numbers = listOf("one", "two", "three", "four")
    val modifiedFirstItem = numbers.first().let { firstItem ->
        println("The first item of the list is '$firstItem'")
        if (firstItem.length >= 5) firstItem else "!" + firstItem + "!"
    }.toUpperCase()
    println("First item after modifications: '$modifiedFirstItem'")
//sampleEnd
}
```



### `with`

一个非扩展函数：**上下文对象**作为参数传递，但是在 lambda 表达式内部，它可以作为接收者（`this`）使用。 **返回值**是 lambda 表达式结果。

我们建议使用 `with` 来调用上下文对象上的函数，而不使用 lambda 表达式结果。 在代码中，`with` 可以理解为“*对于这个对象，执行以下操作。*”



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



`with` 的另一个使用场景是引入一个辅助对象，其属性或函数将用于计算一个值。



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



### `run`

**上下文对象** 作为接收者（`this`）来访问。 **返回值** 是 lambda 表达式结果。

`run` 和 `with` 做同样的事情，但是调用方式和 `let` 一样——作为上下文对象的扩展函数.

当 lambda 表达式同时包含对象初始化和返回值的计算时，`run` 很有用。



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



除了在接收者对象上调用 `run` 之外，还可以将其用作非扩展函数。 非扩展 `run` 可以使你在需要表达式的地方执行一个由多个语句组成的块。



```kotlin
fun main() {
//sampleStart
    val hexNumberRegex = run {
        val digits = "0-9"
        val hexDigits = "A-Fa-f"
        val sign = "+-"

        Regex("[$sign]?[$digits$hexDigits]+")
    }

    for (match in hexNumberRegex.findAll("+1234 -FFFF not-a-number")) {
        println(match.value)
    }
//sampleEnd
}
```



### `apply`

**上下文对象** 作为接收者（`this`）来访问。 **返回值** 是上下文对象本身。

对于不返回值且主要在接收者（`this`）对象的成员上运行的代码块使用 `apply`。`apply` 的常见情况是对象配置。这样的调用可以理解为“*将以下赋值操作应用于对象*”。



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



将接收者作为返回值，你可以轻松地将 `apply` 包含到调用链中以进行更复杂的处理。

### `also`

**上下文对象**作为 lambda 表达式的参数（`it`）来访问。 **返回值**是上下文对象本身。

`also` 对于执行一些将上下文对象作为参数的操作很有用。 对于需要引用对象而不是其属性与函数的操作，或者不想屏蔽来自外部作用域的 `this` 引用时，请使用 `also`。

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



## 函数选择

为了帮助你选择合适的作用域函数，我们提供了它们之间的主要区别表。

|函数|对象引用|返回值|是否是扩展函数|
|---|---|---|---|
|`let`|`it`|Lambda 表达式结果|是|
|`run`|`this`|Lambda 表达式结果|是|
|`run`|-|Lambda 表达式结果|不是：调用无需上下文对象|
|`with`|`this`|Lambda 表达式结果|不是：把上下文对象当做参数|
|`apply`|`this`|上下文对象|是|
|`also`|`it`|上下文对象|是|

以下是根据预期目的选择作用域函数的简短指南：

* 对一个非空（non-null）对象执行 lambda 表达式：`let`
* 将表达式作为变量引入为局部作用域中：`let`
* 对象配置：`apply`
* 对象配置并且计算结果：`run`
* 在需要表达式的地方运行语句：非扩展的 `run`
* 附加效果：`also`
* 一个对象的一组函数调用：`with`

不同函数的使用场景存在重叠，你可以根据项目或团队中使用的特定约定选择函数。

尽管作用域函数是使代码更简洁的一种方法，但请避免过度使用它们：这会降低代码的可读性并可能导致错误。避免嵌套作用域函数，同时链式调用它们时要小心：此时很容易对当前上下文对象及 `this` 或 `it` 的值感到困惑。

## `takeIf` 与 `takeUnless`

除了作用域函数外，标准库还包含函数 `takeIf` 及 `takeUnless`。这俩函数使你可以将对象状态检查嵌入到调用链中。

当以提供的谓词在对象上进行调用时，若该对象与谓词匹配，则 `takeIf` 返回此对象。否则返回 `null`。因此，`takeIf` 是单个对象的过滤函数。反之，`takeUnless`如果不匹配谓词，则返回对象，如果匹配则返回 `null`。该对象作为 lambda 表达式参数（`it`）来访问。



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



当在 `takeIf` 及 `takeUnless` 之后链式调用其他函数，不要忘记执行空检查或安全调用（`?.`），因为他们的返回值是可为空的。



```kotlin
fun main() {
//sampleStart
    val str = "Hello"
    val caps = str.takeIf { it.isNotEmpty() }?.toUpperCase()
   //val caps = str.takeIf { it.isNotEmpty() }.toUpperCase() // 编译错误
    println(caps)
//sampleEnd
}
```



`takeIf` 及 `takeUnless` 与作用域函数一起特别有用。 一个很好的例子是用 `let` 链接它们，以便在与给定谓词匹配的对象上运行代码块。 为此，请在对象上调用 `takeIf`，然后通过安全调用（`?.`）调用 `let`。对于与谓词不匹配的对象，`takeIf` 返回 `null`，并且不调用 `let`。



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



没有标准库函数时，相同的函数看起来是这样的：



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


