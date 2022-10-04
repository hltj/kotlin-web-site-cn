[//]: # (title: 字符)

字符用 `Char` 类型表示。 字符字面值用单引号括起来: `'1'`。

特殊字符可以以转义反斜杠 `\` 开始。
支持这几个转义序列： 

* `\t` – tab
* `\b` – backspace
* `\n` – new line (LF)
* `\r` – carriage return (CR)
* `\'` – single quotation mark
* `\"` – double quotation mark
* `\\` – backslash
* `\$` – dollar sign

编码其他字符要用 Unicode 转义序列语法：`'\uFF00'`。

```kotlin
fun main() {
//sampleStart
    val aChar: Char = 'a'
 
    println(aChar)
    println('\n') // Prints an extra newline character
    println('\uFF00')
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

If a value of character variable is a digit, you can explicitly convert it to an `Int` number using the [`digitToInt()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/digit-to-int.html) function.

> **On JVM**: Like [numbers](numbers.md#jvm-平台的数字表示), characters are boxed when a nullable reference is needed.
> Identity is not preserved by the boxing operation.
>
{type="note"}