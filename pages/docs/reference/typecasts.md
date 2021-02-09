---
type: doc
layout: reference
category: "Syntax"
title: "类型检测与类型转换：“is”与“as”"
---

# 类型检测与类型转换：“is”与“as”

## `is` 与 `!is` 操作符

我们可以在运行时通过使用 `is` 操作符或其否定形式 `!is` 来检测对象是否符合给定类型：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
if (obj is String) {
    print(obj.length)
}

if (obj !is String) { // 与 !(obj is String) 相同
    print("Not a String")
} else {
    print(obj.length)
}
```
</div>

## 智能转换

在许多情况下，不需要在 Kotlin 中使用显式转换操作符，因为编译器跟踪<!--
-->不可变值的 `is`-检测以及[显式转换](#不安全的转换操作符)，并在需要时自动插入（安全的）转换：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
fun demo(x: Any) {
    if (x is String) {
        print(x.length) // x 自动转换为字符串
    }
}
```
</div>

编译器足够聪明，能够知道如果反向检测导致返回那么该转换是安全的：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
if (x !is String) return

print(x.length) // x 自动转换为字符串
```
</div>

或者在 `&&` 和 `||` 的右侧：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
// `||` 右侧的 x 自动转换为字符串
if (x !is String || x.length == 0) return

// `&&` 右侧的 x 自动转换为字符串
if (x is String && x.length > 0) {
    print(x.length) // x 自动转换为字符串
}
```
</div>

这些 _智能转换_ 用于 [*when*{: .keyword }-表达式](control-flow.html#when-表达式)
和 [*while*{: .keyword }-循环 ](control-flow.html#while-循环) 也一样：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
when (x) {
    is Int -> print(x + 1)
    is String -> print(x.length + 1)
    is IntArray -> print(x.sum())
}
```
</div>

请注意，当编译器不能保证变量在检测和使用之间不可改变时，智能转换不能用。
更具体地，智能转换能否适用根据以下规则：

  * *val*{: .keyword } 局部变量——总是可以，[局部委托属性除外](delegated-properties.html#局部委托属性)；
  * *val*{: .keyword } 属性——如果属性是 private 或 internal，或者该检测在声明属性的同一[模块](visibility-modifiers.html#模块)中执行。智能转换不适用于 open 的属性或者具有自定义 getter 的属性；
  * *var*{: .keyword } 局部变量——如果变量在检测和使用之间没有修改、没有在会修改它的 lambda 中捕获、并且不是局部委托属性；
  * *var*{: .keyword } 属性——决不可能（因为该变量可以随时被其他代码修改）。

{:#不安全的转换操作符}

## “不安全的”转换操作符

通常，如果转换是不可能的，转换操作符会抛出一个异常。因此，我们称之为*不安全的*。
Kotlin 中的不安全转换由中缀操作符 *as*{: .keyword }（参见[operator precedence](grammar.html#expressions)）完成：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
val x: String = y as String
```
</div>

请注意，*null*{: .keyword } 不能转换为 `String` 因该类型不是[可空的](null-safety.html)，
即如果 `y` 为空，上面的代码会抛出一个异常。
为了让这样的代码用于可空值，请在类型转换的右侧使用可空类型：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
val x: String? = y as String?
```
</div>

Please note that the "unsafe" cast operator **is not equivalent** to the [`unsafeCast<T>()`](/api/latest/jvm/stdlib/kotlin.js/unsafe-cast.html) method available in Kotlin/JS. `unsafeCast` will do no type-checking at all, whereas the _cast operator_ throws a `ClassCastException` when the cast fails. 

{:#安全的可空转换操作符}

## “安全的”（可空）转换操作符

为了避免抛出异常，可以使用*安全*转换操作符 *as?*{: .keyword }，它可以在失败时返回 *null*{: .keyword }：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
val x: String? = y as? String
```
</div>

请注意，尽管事实上 *as?*{: .keyword } 的右边是一个非空类型的 `String`，但是其转换的结果是可空的。

## 类型擦除与泛型检测

Kotlin 在编译时确保涉及[泛型](generics.html)操作的类型安全性，
而在运行时，泛型类型的实例并未带有关于它们实际类型参数的信息。例如，
`List<Foo>` 会被擦除为 `List<*>`。通常，在运行时无法检测一个实例是否属于带有某个类型参数的泛型类型<!--
-->。

为此，编译器会禁止由于类型擦除而无法执行的 *is*{: .keyword } 检测，例如
`ints is List<Int>` 或者 `list is T`（类型参数）。当然，你可以对一个实例检测[星投影的类型](generics.html#星投影)：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
if (something is List<*>) {
    something.forEach { println(it) } // 这些项的类型都是 `Any?`
}
```
</div>

类似地，当已经让一个实例的类型参数（在编译期）静态检测，
就可以对涉及非泛型部分做 *is*{: .keyword } 检测或者类型转换。请注意，
在这种情况下，会省略尖括号：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
fun handleStrings(list: List<String>) {
    if (list is ArrayList) {
        // `list` 会智能转换为 `ArrayList<String>`
    }
}
```
</div>

省略类型参数的这种语法可用于不考虑类型参数的类型转换：`list as ArrayList`。

带有[具体化的类型参数](inline-functions.html#具体化的类型参数)的内联函数使其类型实参<!--
-->在每个调用处内联，这就能够对类型参数进行 `arg is T` 检测，但是如果 `arg`
自身是一个泛型实例，**其**类型参数还是会被擦除。例如：

<div class="sample" markdown="1" theme="idea">

```kotlin
//sampleStart
inline fun <reified A, reified B> Pair<*, *>.asPairOf(): Pair<A, B>? {
    if (first !is A || second !is B) return null
    return first as A to second as B
}

val somePair: Pair<Any?, Any?> = "items" to listOf(1, 2, 3)

val stringToSomething = somePair.asPairOf<String, Any>()
val stringToInt = somePair.asPairOf<String, Int>()
val stringToList = somePair.asPairOf<String, List<*>>()
val stringToStringList = somePair.asPairOf<String, List<String>>() // 破坏类型安全！
//sampleEnd

fun main() {
    println("stringToSomething = " + stringToSomething)
    println("stringToInt = " + stringToInt)
    println("stringToList = " + stringToList)
    println("stringToStringList = " + stringToStringList)
}
```
</div>

## 非受检类型转换

如上所述，类型擦除使运行时不可能对泛型类型实例的类型实参进行检测，并且<!--
-->代码中的泛型可能相互连接不够紧密，以致于编译器无法确保<!--
-->类型安全。

即便如此，有时候我们有高级的程序逻辑来暗示类型安全。例如：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin 
fun readDictionary(file: File): Map<String, *> = file.inputStream().use { 
    TODO("Read a mapping of strings to arbitrary elements.")
}

// 我们已将存有一些 `Int` 的映射保存到该文件
val intsFile = File("ints.dictionary")

// Warning: Unchecked cast: `Map<String, *>` to `Map<String, Int>`
val intsDictionary: Map<String, Int> = readDictionary(intsFile) as Map<String, Int>
```
</div>

编译器会对最后一行的类型转换产生一个警告。该类型转换不能在运行时完全检测，并且<!--
-->不能保证映射中的值是“Int”。

为避免未受检类型转换，可以重新设计程序结构：在上例中，可以使用具有类型安全实现的不同接口
`DictionaryReader<T>` 与 `DictionaryWriter<T>`。
可以引入合理的抽象，将未受检的类型转换从调用代码移动到实现细节中。
正确使用[泛型型变](generics.html#型变)也有帮助。
 
对于泛型函数，使用[具体化的类型参数](inline-functions.html#具体化的类型参数)可以使<!--
-->诸如 `arg as T` 这样的类型转换受检，除非 `arg` 对应类型的*自身*类型参数已被擦除。

可以通过在产生警告的语句或声明上用注解 `@Suppress("UNCHECKED_CAST")`
[标注](annotations.html#注解)来禁止未受检类型转换警告：

<div class="sample" markdown="1" theme="idea" data-highlight-only auto-indent="false">

```kotlin
inline fun <reified T> List<*>.asListOfType(): List<T>? =
    if (all { it is T })
        @Suppress("UNCHECKED_CAST")
        this as List<T> else
        null
```
</div>

IntelliJ IDEA can also automatically generate the `@Suppress` annotation. Open the intentions menu via the light bulb icon or Alt-Enter, and click the small arrow next to the "Change type arguments" quick-fix. Here, you can select the suppression scope, and your IDE will add the annotation to your file accordingly.

在 JVM 平台中，[数组类型](basic-types.html#数组)（`Array<Foo>`）会保留关于<!--
-->其元素被擦除类型的信息，并且类型转换为一个数组类型可以部分受检：
元素类型的可空性与类型实参仍然会被擦除。例如，
如果 `foo` 是一个保存了任何 `List<*>`（无论可不可空）的数组的话，类型转换 `foo as Array<List<String>?>` 都会成功。
