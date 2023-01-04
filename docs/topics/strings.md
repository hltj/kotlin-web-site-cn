[//]: # (title: 字符串)

Kotlin 中字符串用 `String` 类型表示。 通常，字符串值是双引号（`"`）中的字符序列：

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
    println(str.uppercase()) // 创建并输出一个新的 String 对象
    println(str) // 原始字符串保持不变
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
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

> 在大多数情况下，优先使用[字符串模板](#字符串模板)或[原始字符串](#原始字符串)而不是字符串连接。
> 
{type="note"}

## 字符串字面值

Kotlin 有两种类型的字符串字面值:

* [转义字符串](#转义字符串)
* [原始字符串](#原始字符串)

### 转义字符串

*转义字符串*可以包含转义字符。  
以下是转义字符串的一个示例：

```kotlin
val s = "Hello, world!\n"
```

转义采用传统的反斜杠（`\`）方式。  
所支持的转义序列请参见 [字符](characters.md) 页。

### 原始字符串

*原始字符串*可以包含换行以及任意文本。 它使用三个引号（`"""`）分界符括起来，内部没有转义并且可以包含换行以及任何其他字符：

```kotlin
val text = """
    for (c in "foo")
        print(c)
"""
```

如需删掉原始字符串中的前导空格，请使用 [`trimMargin()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/trim-margin.html) 函数：

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
模板表达式以美元符（`$`）开头，要么由一个的名称构成:

```kotlin
fun main() {
//sampleStart
    val i = 10
    println("i = $i") // 输出“i = 10”
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

要么是用花括号括起来的表达式:

```kotlin
fun main() {
//sampleStart
    val s = "abc"
    println("$s.length is ${s.length}") // 输出 "abc.length is 3"
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

在原始字符串及转义字符串中都可以使用模板。
如需在原始字符串（不支持反斜杠转义）中的<!--
-->允许作为[标识符（identifier）](https://kotlinlang.org/docs/reference/grammar.html#identifiers)开头的任意符号之前插入美元符 `$`，
请使用以下语法：s

```kotlin
val price = """
${'$'}_9.99
"""
```