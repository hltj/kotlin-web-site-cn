[//]: # (title: 集合转换操作)

Kotlin 标准库为集合 _转换_ 提供了一组扩展函数。
这些函数根据提供的转换规则从现有集合中构建新集合。
在此页面中，我们将概述可用的集合转换函数。

## 映射

_映射_ 转换从另一个集合的元素上的函数结果创建一个集合。
基本的映射函数是 [`map()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/map.html)。
它将给定的 lambda 函数应用于每个后续元素，并返回 lambda 结果列表。
结果的顺序与元素的原始顺序相同。
如需应用还要用到元素索引作为参数的转换，请使用 [`mapIndexed()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/map-indexed.html)。

```kotlin

fun main() {
//sampleStart
    val numbers = setOf(1, 2, 3)
    println(numbers.map { it * 3 })
    println(numbers.mapIndexed { idx, value -> value * idx })
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

如果转换在某些元素上产生 `null` 值，那么可以通过<!--
-->调用 [`mapNotNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/map-not-null.html) 函数<!--
-->取代 `map()` 或 [`mapIndexedNotNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/map-indexed-not-null.html)
取代 `mapIndexed()` 来从结果集中过滤掉 `null` 值。

```kotlin

fun main() {
//sampleStart
    val numbers = setOf(1, 2, 3)
    println(numbers.mapNotNull { if ( it == 2) null else it * 3 })
    println(numbers.mapIndexedNotNull { idx, value -> if (idx == 0) null else value * idx })
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

映射转换时，有两个选择：转换键，使值保持不变，反之亦然。
要将指定转换应用于键，请使用 [`mapKeys()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/map-keys.html)；
反之，[`mapValues()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/map-values.html) 转换值。
这两个函数都使用将映射条目作为参数的转换，因此可以操作其键与值。

```kotlin

fun main() {
//sampleStart
    val numbersMap = mapOf("key1" to 1, "key2" to 2, "key3" to 3, "key11" to 11)
    println(numbersMap.mapKeys { it.key.uppercase() })
    println(numbersMap.mapValues { it.value + it.key.length })
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

## 合拢

_合拢_ 转换是根据两个集合中具有相同位置的元素构建配对。
在 Kotlin 标准库中，这是通过 [`zip()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/zip.html)
扩展函数完成的。

在一个集合（或数组）上以另一个集合（或数组）作为参数调用时，`zip()` 返回 `Pair` 对象的列表（`List`）。
接收者集合的元素是这些配对中的第一个元素。

如果集合的大小不同，则 `zip()` 的结果为较小集合的大小；结果中不包含较大<!--
-->集合的后续元素。

`zip()` 也可以中缀形式调用 `a zip b` 。

```kotlin

fun main() {
//sampleStart
    val colors = listOf("red", "brown", "grey")
    val animals = listOf("fox", "bear", "wolf")
    println(colors zip animals)

    val twoAnimals = listOf("fox", "bear")
    println(colors.zip(twoAnimals))
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

也可以使用带有两个参数的转换函数来调用 `zip()`：接收者元素和参数元素。
在这种情况下，结果 `List` 包含在具有相同位置的<!--
-->接收者对和参数元素对上调用的转换函数的返回值。

```kotlin

fun main() {
//sampleStart
    val colors = listOf("red", "brown", "grey")
    val animals = listOf("fox", "bear", "wolf")
    
    println(colors.zip(animals) { color, animal -> "The ${animal.replaceFirstChar { it.uppercase() }} is $color"})
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

当拥有 `Pair` 的 `List` 时，可以进行反向转换 _unzipping_——从这些键值对中构建两个列表<!--
-->：

* 第一个列表包含原始列表中每个 `Pair` 的键。
* 第二个列表包含原始列表中每个 `Pair` 的值。

要分割键值对列表，请调用 [`unzip()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/unzip.html)。

```kotlin

fun main() {
//sampleStart
    val numberPairs = listOf("one" to 1, "two" to 2, "three" to 3, "four" to 4)
    println(numberPairs.unzip())
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

## 关联

_关联_ 转换允许从集合元素和与其关联的某些值构建 Map。
在不同的关联类型中，元素可以是关联 Map 中的键或值。

基本的关联函数 [`associateWith()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/associate-with.html)
创建一个 `Map`，其中原始集合的元素是键，并通过给定的转换函数从中产生值。
如果两个元素相等，则仅最后一个保留在 Map 中。

```kotlin

fun main() {
//sampleStart
    val numbers = listOf("one", "two", "three", "four")
    println(numbers.associateWith { it.length })
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

为了使用集合元素作为值来构建 Map，有一个函数 [`associateBy()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/associate-by.html)。
它需要一个函数，该函数根据元素的值返回键。如果两个元素的键相等，则仅最后一个保留在
Map 中。

还可以使用值转换函数来调用 `associateBy()`。

```kotlin

fun main() {
//sampleStart
    val numbers = listOf("one", "two", "three", "four")

    println(numbers.associateBy { it.first().uppercaseChar() })
    println(numbers.associateBy(keySelector = { it.first().uppercaseChar() }, valueTransform = { it.length }))
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

另一种构建 Map 的方法是使用函数 [`associate()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/associate.html)，其中 Map 键和值都是通过集合元素生成的。
它需要一个 lambda 函数，该函数返回 `Pair`：键和相应 Map 条目的值。

请注意，`associate()` 会生成临时的 `Pair` 对象，这可能会影响性能。
因此，当性能不是很关键或比其他选项更可取时，应使用 `associate()`。

后者的一个示例：从一个元素一起生成键和相应的值。

```kotlin

fun main() {
data class FullName (val firstName: String, val lastName: String)

fun parseFullName(fullName: String): FullName {
    val nameParts = fullName.split(" ")
    if (nameParts.size == 2) {
        return FullName(nameParts[0], nameParts[1])
    } else throw Exception("Wrong name format")
}

//sampleStart
    val names = listOf("Alice Adams", "Brian Brown", "Clara Campbell")
    println(names.associate { name -> parseFullName(name).let { it.lastName to it.firstName } })  
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

此时，首先在一个元素上调用一个转换函数，然后根据该函数结果的属性建立 Pair。

## 展平

如需操作嵌套的集合，那么可能会发现提供对嵌套集合元素进行打平访问的标准库函数<!--
-->很有用。

第一个函数为 [`flatten()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/flatten.html)。
可以在一个集合的集合（例如，一个 `Set` 组成的 `List`）上调用它。
该函数返回嵌套集合中的所有元素的一个 `List`。

```kotlin

fun main() {
//sampleStart
    val numberSets = listOf(setOf(1, 2, 3), setOf(4, 5, 6), setOf(1, 2))
    println(numberSets.flatten())
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

另一个函数——[`flatMap()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/flat-map.html) 提供了一种灵活的方式来处理嵌套的集合。
它需要一个函数将一个集合元素映射到另一个集合。
因此，`flatMap()` 返回单个列表其中包含所有元素的值。
所以，`flatMap()` 表现为 `map()`（以集合作为映射结果）与 `flatten()` 的连续调用。

```kotlin

data class StringContainer(val values: List<String>)

fun main() {
//sampleStart
    val containers = listOf(
        StringContainer(listOf("one", "two", "three")),
        StringContainer(listOf("four", "five", "six")),
        StringContainer(listOf("seven", "eight"))
    )
    println(containers.flatMap { it.values })
//sampleEnd
}

```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

## 字符串表示

如果需要以可读格式检索集合内容，请使用将集合转换为字符串的函数<!--
-->：[`joinToString()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/join-to-string.html) 与
[`joinTo()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/join-to.html)。

`joinToString()` 根据提供的参数从集合元素构建单个 `String`。
`joinTo()` 执行相同的操作，但将结果附加到给定的 [`Appendable`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/-appendable/index.html) 对象。

当使用默认参数调用时，函数返回的结果类似于在集合上调用 `toString()`：
各元素的字符串表示形式以空格分隔而成的 `String`。 

```kotlin

fun main() {
//sampleStart
    val numbers = listOf("one", "two", "three", "four")
    
    println(numbers)         
    println(numbers.joinToString())
    
    val listString = StringBuffer("The list of numbers: ")
    numbers.joinTo(listString)
    println(listString)
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

要构建自定义字符串表示形式，可以在函数参数 `separator`、`prefix` 与 `postfix`中指定其参数。
结果字符串将以 `prefix` 开头，以 `postfix` 结尾。除最后一个元素外，`separator` 将位于<!--
-->每个元素之后。

```kotlin

fun main() {
//sampleStart
    val numbers = listOf("one", "two", "three", "four")    
    println(numbers.joinToString(separator = " | ", prefix = "start: ", postfix = ": end"))
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

对于较大的集合，可能需要指定 `limit` ——将包含在结果中元素的数量。
如果集合大小超出 `limit`，所有其他元素将被 `truncated` 参数的单个值替换<!--
-->。

```kotlin

fun main() {
//sampleStart
    val numbers = (1..100).toList()
    println(numbers.joinToString(limit = 10, truncated = "<...>"))
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

最后，要自定义元素本身的表示形式，请提供 `transform` 函数。

```kotlin

fun main() {
//sampleStart
    val numbers = listOf("one", "two", "three", "four")
    println(numbers.joinToString { "Element: ${it.uppercase()}"})
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}
