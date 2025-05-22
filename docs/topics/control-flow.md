[//]: # (title: 条件与循环)

## If 表达式

在 Kotlin 中，`if` 是一个表达式：它会返回一个值。
因此就不需要三元运算符（`条件 ? 然后 : 否则`），因为普通的 `if` 就能胜任这个角色。

```kotlin
fun main() {
    val a = 2
    val b = 3

    //sampleStart
    var max = a
    if (a < b) max = b

    // With else
    if (a > b) {
      max = a
    } else {
      max = b
    }

  // 作为表达式
 max = if (a > b) a else b

    // You can also use `else if` in expressions:
    val maxLimit = 1
    val maxOrLimit = if (maxLimit > a) maxLimit else if (a > b) a else b
  
    println("max is $max")
    // max is 3
    println("maxOrLimit is $maxOrLimit")
    // maxOrLimit is 3
    //sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="if-else-if-kotlin"}

`if` 表达式的分支可以是代码块，这种情况最后的表达式作为该块的值：

```kotlin
val max = if (a > b) {
    print("Choose a")
    a
} else {
    print("Choose b")
    b
}
```

If you're using `if` as an expression, for example, for returning its value or
assigning it to a variable, the `else` branch is mandatory.

## When expressions and statements

`when` is a conditional expression that runs code based on multiple possible values or conditions. It is
similar to the `switch` statement in Java, C, and similar languages. For example:

```kotlin
fun main() {
    //sampleStart
    val x = 2
    when (x) {
        1 -> print("x == 1")
        2 -> print("x == 2")
        else -> print("x is neither 1 nor 2")
    }
    // x == 2
    //sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-conditions-when-statement"}

`when` 将它的参数与所有的分支条件顺序比较，直到某个分支满足条件。

You can use `when` in a few different ways. Firstly, you can use `when` either as an **expression** or as a **statement**.
As an expression, `when` returns a value for later use in your code. As a statement, `when` completes an action
without returning anything of further use:

<table>
   <tr>
       <td>Expression</td>
       <td>Statement</td>
   </tr>
   <tr>
<td>

```kotlin
// Returns a string assigned to the 
// text variable
val text = when (x) {
    1 -> "x == 1"
    2 -> "x == 2"
    else -> "x is neither 1 nor 2"
}
```

</td>
<td>

```kotlin
// Returns nothing but triggers a 
// print statement
when (x) {
    1 -> print("x == 1")
    2 -> print("x == 2")
    else -> print("x is neither 1 nor 2")
}
```

</td>
</tr>
</table>

Secondly, you can use `when` with or without a subject. Whether you use a subject with `when` or not, your expression or
statement behaves the same. We recommend using `when` with a subject when possible, as it makes your code easier to read
and maintain by clearly showing what you're checking.

<table>
   <tr>
       <td>With subject <code>x</code></td>
       <td>Without subject</td>
   </tr>
   <tr>
<td>

```kotlin
when(x) { ... }
```

</td>
<td>

```kotlin
when { ... }
```

</td>
</tr>
</table>

Depending on how you use `when`, there are different requirements for whether you need to cover all possible cases in your
branches.

If you use `when` as a statement, you don't have to cover all possible cases. In this example, some cases aren't covered,
so nothing happens. However, no error occurs:

```kotlin
fun main() {
    //sampleStart
    val x = 3
    when (x) {
        // Not all cases are covered
        1 -> print("x == 1")
        2 -> print("x == 2")
    }
    //sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-when-statement"}

In a `when` statement, the values of individual branches are ignored. Just like with `if`, each branch can be a block, 
and its value is the value of the last expression in the block.

If you use `when` as an expression, you have to cover all possible cases. In other words, it must be _exhaustive_.
The value of the first matching branch becomes the value of the overall expression. If you don't cover all cases, 
the compiler throws an error.

If your `when` expression has a subject, you can use an `else` branch to make sure that all possible cases are covered, but
it isn't mandatory. For example, if your subject is a `Boolean`, [`enum` class](enum-classes.md), [`sealed` class](sealed-classes.md),
or one of their nullable counterparts, you can cover all cases without an `else` branch:

```kotlin
enum class Bit {
    ZERO, ONE
}

val numericValue = when (getRandomBit()) {
    // No else branch is needed because all cases are covered
    Bit.ZERO -> 0
    Bit.ONE -> 1
}
```

If your `when` expression **doesn't** have a subject, you **must** have an `else` branch or the compiler throws an error.
The `else` branch is evaluated when none of the other branch conditions are satisfied:

```kotlin
val message = when {
    a > b -> "a is greater than b"
    a < b -> "a is less than b"
    else -> "a is equal to b"
}
```

`when` expressions and statements offer different ways to simplify your code, handle multiple conditions, and perform 
type checks.

You can define a common behavior for multiple cases by combining their conditions in a single line with a comma: 

```kotlin
when (x) {
    0, 1 -> print("x == 0 or x == 1")
    else -> print("otherwise")
}
```

可以用任意表达式（而不只是常量）作为分支条件：

```kotlin
when (x) {
    s.toInt() -> print("s encodes x")
    else -> print("s does not encode x")
}
```

You can also check whether a value is or isn't contained in a [range](ranges.md) or collection via the `in` or `!in` keywords:

```kotlin
when (x) {
    in 1..10 -> print("x is in the range")
    in validNumbers -> print("x is valid")
    !in 10..20 -> print("x is outside the range")
    else -> print("none of the above")
}
```

Additionally, you can check that a value is or isn't a particular type via the `is` or `!is` keywords. 注意：
由于[智能转换](typecasts.md#智能转换)，可以访问该类型的成员函数与属性而无需<!--
-->任何额外的检测。

```kotlin
fun hasPrefix(x: Any) = when(x) {
    is String -> x.startsWith("prefix")
    else -> false
}
```

可以用 `when` 来取代 `if`-`else` `if` 链。
如果没有主语（subject，译注：指 `when` 所判断的表达式），所有的分支条件都是简单的布尔表达式。第一个条件为 `true` 的分支会执行：

```kotlin
when {
    x.isOdd() -> print("x is odd")
    y.isEven() -> print("y is even")
    else -> print("x+y is odd")
}
```

可以使用以下语法将主语捕获到变量中：

```kotlin
fun Request.getBody() =
    when (val response = executeRequest()) {
        is Success -> response.body
        is HttpError -> throw HttpException(response.status)
    }
```

作为主语引入的变量的作用域仅限于 `when` 表达式或语句的主体。

### Guard conditions in when expressions

> Guard conditions are an [experimental feature](components-stability.md#stability-levels-explained) that may be changed at any time.
> We would appreciate your feedback in [YouTrack](https://youtrack.jetbrains.com/issue/KT-71140/Guard-conditions-in-when-expressions-feedback).
>
{style="warning"}

Guard conditions allow you to include 
more than one condition to the branches of a `when` expression, making complex control flow more explicit and concise.
You can use guard conditions in `when` expressions or statements with a subject.

To include a guard condition in a branch, place it after the primary condition, separated by `if`:

```kotlin
sealed interface Animal {
    data class Cat(val mouseHunter: Boolean) : Animal
    data class Dog(val breed: String) : Animal
}

fun feedAnimal(animal: Animal) {
    when (animal) {
        // Branch with only primary condition. Calls `feedDog()` when `animal` is `Dog`
        is Animal.Dog -> feedDog()
        // Branch with both primary and guard conditions. Calls `feedCat()` when `animal` is `Cat` and not `mouseHunter`
        is Animal.Cat if !animal.mouseHunter -> feedCat()
        // Prints "Unknown animal" if none of the above conditions match
        else -> println("Unknown animal")
    }
}
```

In a single `when` expression, you can combine branches with and without guard conditions. 
The code in a branch with a guard condition runs only if both the primary condition and the guard condition evaluate to true.
If the primary condition does not match, the guard condition is not evaluated. 

If you use guard conditions in `when` statements without an `else` branch, and none of the conditions matches, none of the branches is executed. 

Otherwise, if you use guard conditions in `when` expressions without an `else` branch, the compiler requires you to declare all the possible cases to avoid runtime errors.

Additionally, guard conditions support `else if`:

```kotlin
when (animal) {
    // Checks if `animal` is `Dog`
    is Animal.Dog -> feedDog()
    // Guard condition that checks if `animal` is `Cat` and not `mouseHunter`
    is Animal.Cat if !animal.mouseHunter -> feedCat()
    // Calls giveLettuce() if none of the above conditions match and animal.eatsPlants is true
    else if animal.eatsPlants -> giveLettuce()
    // Prints "Unknown animal" if none of the above conditions match
    else -> println("Unknown animal")
}
```

Combine multiple guard conditions within a single branch using the boolean operators `&&` (AND) or `||` (OR).
Use parentheses around the boolean expressions to [avoid confusion](coding-conventions.md#guard-conditions-in-when-expression):

```kotlin
when (animal) {
    is Animal.Cat if (!animal.mouseHunter && animal.hungry) -> feedCat()
}
```

You can use guard conditions in any `when` expression or statement with a subject, except the case when you have multiple conditions separated by a comma.
For example, `0, 1 -> print("x == 0 or x == 1")`.

> To enable guard conditions in CLI, run the following command:
>
> `kotlinc -Xwhen-guards main.kt`
>
> To enable guard conditions in Gradle, add the following line to the `build.gradle.kts` file:
>
> `kotlin.compilerOptions.freeCompilerArgs.add("-Xwhen-guards")`
>
{style="note"}

## For 循环

`for` 循环可以对任何提供迭代器（iterator）的对象进行遍历，这相当于像 C# 这样的语言中的 `foreach` 循环。
`for` 的语法如下所示：

```kotlin
for (item in collection) print(item)
```

`for` 循环体可以是一个代码块。

```kotlin
for (item: Int in ints) {
    // ……
}
```

如上所述，`for` 可以循环遍历任何提供了迭代器的对象。这意味着：

* 有一个成员函数或者扩展函数 `iterator()` 返回 `Iterator<>`，其中：
  * 有一个成员函数或者扩展函数 `next()`
  * 有一个成员函数或者扩展函数 `hasNext()` 返回 `Boolean`。

这三个函数都需要标记为 `operator`。

如需在数字区间上迭代，请使用[区间表达式](ranges.md):

```kotlin
fun main() {
//sampleStart
    for (i in 1..3) {
        print(i)
    }
    for (i in 6 downTo 0 step 2) {
        print(i)
    }
    // 1236420
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

对区间或者数组的 `for` 循环会被编译为并不创建迭代器的基于索引的循环。

如果你想要通过索引遍历一个数组或者一个 list，你可以这么做：

```kotlin
fun main() {
val array = arrayOf("a", "b", "c")
//sampleStart
    for (i in array.indices) {
        print(array[i])
    }
    // abc
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

或者你可以用库函数 `withIndex`：

```kotlin
fun main() {
    val array = arrayOf("a", "b", "c")
//sampleStart
    for ((index, value) in array.withIndex()) {
        println("the element at $index is $value")
    }
    // the element at 0 is a
    // the element at 1 is b
    // the element at 2 is c
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

## while 循环

`while` 和 `do-while` 当循环条件满足时会持续处理它们的主体。
它们之间的区别在于条件检查的时间：
* `while` 先检查条件，如果满足，则处理主体，然后再返回到条件检查。
* `do-while` 先处理主体，然后检查条件。如果满足，则循环重复。所以 `do-while` 的主体<!--
-->至少运行一次，不管条件如何。 

```kotlin
while (x > 0) {
    x--
}

do {
  val y = retrieveData()
} while (y != null) // y 在此处可见
```

## 循环中的 break 与 continue

在循环中 Kotlin 支持传统的 `break` 与 `continue` 操作符。参见[返回与跳转](returns.md)。
