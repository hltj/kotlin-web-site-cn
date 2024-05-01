[//]: # (title: 字符串)

Kotlin 中字符串用 [`String`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-string/) 类型表示。

> On the JVM, an object of `String` type in UTF-16 encoding uses approximately 2 bytes per character.
> 
{type="note"}

通常，字符串值是双引号（`"`）中的字符序列：

```kotlin
val str = "abcd 123"
```

字符串的元素——字符可以使用索引运算符访问: `s[i]`。
可以使用 `for` 循环遍历这些字符：

```kotlin
fun main() {
    val str = "abcd" 
//sampleStart
    for (c in str) {
        println(c)
    }
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

字符串是不可变的。 一旦初始化了一个字符串，就不能改变它的值或者给它赋新值。
所有转换字符串的操作都以一个新的 `String` 对象来返回结果，而保持原始字符串不变：

```kotlin
fun main() {
//sampleStart
    val str = "abcd"
   
    // 创建并输出一个新的 String 对象
    println(str.uppercase())
    // ABCD
   
    // 原始字符串保持不变
    println(str) 
    // abcd
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

如需连接字符串，可以用 `+` 操作符。这也适用于连接字符串与其他类型的值，
只要表达式中的第一个元素是字符串：

```kotlin
fun main() {
//sampleStart
    val s = "abc" + 1
    println(s + "def")
    // abc1def    
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

> 在大多数情况下，优先使用[字符串模板](#字符串模板)或[多行字符串](#多行字符串)而不是字符串连接。
> 
{type="note"}

## 字符串字面值

Kotlin 有两种类型的字符串字面值:

* [转义字符串](#转义字符串)
* [多行字符串](#多行字符串)

### 转义字符串

*转义字符串*可以包含转义字符。  
以下是转义字符串的一个示例：

```kotlin
val s = "Hello, world!\n"
```

转义采用传统的反斜杠（`\`）方式。  
所支持的转义序列请参见 [字符](characters.md) 页。

### 多行字符串

*多行字符串*可以包含换行以及任意文本。 它使用三个引号（`"""`）分界符括起来，内部没有转义并且可以包含换行以及任何其他字符：

```kotlin
val text = """
    for (c in "foo")
        print(c)
"""
```

如需删掉多行字符串中的前导空格，请使用 [`trimMargin()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/trim-margin.html) 函数：

```kotlin
val text = """
    |Tell me and I forget.
    |Teach me and I remember.
    |Involve me and I learn.
    |(Benjamin Franklin)
    """.trimMargin()
```

默认以竖线 `|` 作为边界前缀，但你可以选择其他字符并作为参数传入，比如 `trimMargin(">")`。

## 字符串模板

字符串字面值可以包含*模板表达式*——一些小段代码，会求值并把结果合并到字符串中。
When a template expression is processed, Kotlin automatically calls the `.toString()` function on the expression's result
to convert it into a string. 模板表达式以美元符（`$`）开头，要么由一个变量名构成:

```kotlin
fun main() {
//sampleStart
    val i = 10
    println("i = $i") 
    // i = 10
    
    val letters = listOf("a","b","c","d","e")
    println("Letters: $letters") 
    // Letters: [a, b, c, d, e]

//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

要么是用花括号括起来的表达式:

```kotlin
fun main() {
//sampleStart
    val s = "abc"
    println("$s.length is ${s.length}") 
    // abc.length is 3
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

在多行字符串及转义字符串中都可以使用模板。
如需在多行字符串（不支持反斜杠转义）中的<!--
-->允许作为[标识符（identifier）](https://kotlinlang.org/docs/reference/grammar.html#identifiers)开头的任意符号之前插入美元符 `$`，
请使用以下语法：s

```kotlin
val price = """
${'$'}_9.99
"""
```

## String formatting

> String formatting with the `String.format()` function is only available in Kotlin/JVM.
>
{type="note"}

To format a string to your specific requirements, use the [`String.format()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/format.html) 
function. 

The `String.format()` function accepts a format string and one or more arguments. The format string contains one placeholder 
(indicated by `%`) for a given argument, followed by format specifiers.
Format specifiers are formatting instructions for the respective argument, consisting of flags, width, precision, and 
conversion type. Collectively, format specifiers shape the output's formatting. Common format specifiers include 
`%d` for integers, `%f` for floating-point numbers, and `%s` for strings. You can also use the `argument_index$` syntax 
to reference the same argument multiple times within the format string in different formats.

> For a detailed understanding and an extensive list of format specifiers, see [Java's Class Formatter documentation](https://docs.oracle.com/javase/8/docs/api/java/util/Formatter.html#summary).
>
{type="note"}

Let's look at an example:

```kotlin
fun main() { 
//sampleStart
    // Formats an integer, adding leading zeroes to reach a length of seven characters
    val integerNumber = String.format("%07d", 31416)
    println(integerNumber)
    // 0031416

    // Formats a floating-point number to display with a + sign and four decimal places
    val floatNumber = String.format("%+.4f", 3.141592)
    println(floatNumber)
    // +3.1416

    // Formats two strings to uppercase, each taking one placeholder
    val helloString = String.format("%S %S", "hello", "world")
    println(helloString)
    // HELLO WORLD
    
    // Formats a negative number to be enclosed in parentheses, then repeats the same number in a different format (without parentheses) using `argument_index$`.
    val negativeNumberInParentheses = String.format("%(d means %1\$d", -31416)
    println(negativeNumberInParentheses)
    //(31416) means -31416
//sampleEnd    
}
```
{interpolate-variables="false" kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

The `String.format()` function provides similar functionality to string templates. However, the 
`String.format()` function is more versatile because there are more formatting options available. 

In addition, you can assign the format string from a variable. This can be useful when the format string changes, 
for example, in localization cases that depend on the user locale.

Be careful when using the `String.format()` function because it can be easy to mismatch the number or position of the 
arguments with their corresponding placeholders.
