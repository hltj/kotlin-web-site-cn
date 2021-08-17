---
type: doc
layout: reference
category: "Collections"
title: "Map 相关操作"
---

# Map 相关操作

在 [map](collections-overview.html#map) 中，键和值的类型都是用户定义的。
对基于键的访问启用了各种特定于 map 的处理函数，从键获取值到对键和值进行单独过滤。
在此页面上，我们提供了来自标准库的 map 处理功能的描述。

## 取键与值

要从 Map 中检索值，必须提供其键作为 [`get()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-map/get.html) 函数的参数。
还支持简写 `[key]` 语法。 如果找不到给定的键，则返回 `null` 。
还有一个函数 [`getValue()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/get-value.html) ，它的行为略有不同：如果在 Map 中找不到键，则抛出异常。
此外，还有两个选项可以解决键缺失的问题：

* [`getOrElse()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/get-or-else.html) 与 list 的工作方式相同：对于不存在的键，其值由给定的 lambda 表达式返回。
* [`getOrDefault()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/get-or-default.html) 如果找不到键，则返回指定的默认值。



```kotlin
fun main() {
//sampleStart
    val numbersMap = mapOf("one" to 1, "two" to 2, "three" to 3)
    println(numbersMap.get("one"))
    println(numbersMap["one"])
    println(numbersMap.getOrDefault("four", 10))
    println(numbersMap["five"])               // null
    //numbersMap.getValue("six")      // exception!
//sampleEnd
}

```


要对 map 的所有键或所有值执行操作，可以从属性 `keys` 和 `values` 中相应地检索它们。 `keys` 是 Map 中所有键的集合， `values` 是 Map 中所有值的集合。



```kotlin
fun main() {
//sampleStart
    val numbersMap = mapOf("one" to 1, "two" to 2, "three" to 3)
    println(numbersMap.keys)
    println(numbersMap.values)
//sampleEnd
}

```


## 过滤

可以使用 [`filter()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter.html) 函数来[过滤](collection-filtering.html) map 或其他集合。
对 map 使用 `filter()` 函数时， `Pair` 将作为参数的谓词传递给它。
它将使用谓词同时过滤其中的键和值。



```kotlin
fun main() {
//sampleStart
    val numbersMap = mapOf("key1" to 1, "key2" to 2, "key3" to 3, "key11" to 11)
    val filteredMap = numbersMap.filter { (key, value) -> key.endsWith("1") && value > 10}
    println(filteredMap)
//sampleEnd
}

```


还有两种用于过滤 map 的特定函数：按键或按值。
这两种方式，都有对应的函数： [`filterKeys()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter-keys.html) 和 [`filterValues()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter-values.html) 。
两者都将返回一个新 Map ，其中包含与给定谓词相匹配的条目。
`filterKeys()` 的谓词仅检查元素键， `filterValues()` 的谓词仅检查值。



```kotlin
fun main() {
//sampleStart
    val numbersMap = mapOf("key1" to 1, "key2" to 2, "key3" to 3, "key11" to 11)
    val filteredKeysMap = numbersMap.filterKeys { it.endsWith("1") }
    val filteredValuesMap = numbersMap.filterValues { it < 10 }

    println(filteredKeysMap)
    println(filteredValuesMap)
//sampleEnd
}

```


## `plus` 与 `minus` 操作

由于需要访问元素的键，[`plus`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/plus.html)（`+`）与 [`minus`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/minus.html)（`-`）运算符对 map 的作用与其他集合不同。
`plus` 返回包含两个操作数元素的 `Map` ：左侧的 Map 与右侧的 Pair 或另一个 Map 。
当右侧操作数中有左侧 `Map` 中已存在的键时，该条目将使用右侧的值。



```kotlin
fun main() {
//sampleStart
    val numbersMap = mapOf("one" to 1, "two" to 2, "three" to 3)
    println(numbersMap + Pair("four", 4))
    println(numbersMap + Pair("one", 10))
    println(numbersMap + mapOf("five" to 5, "one" to 11))
//sampleEnd
}

```


`minus` 将根据左侧 `Map` 条目创建一个新 `Map` ，右侧操作数带有键的条目将被剔除。
因此，右侧操作数可以是单个键或键的集合： list 、 set 等。



```kotlin
fun main() {
//sampleStart
    val numbersMap = mapOf("one" to 1, "two" to 2, "three" to 3)
    println(numbersMap - "one")
    println(numbersMap - listOf("two", "four"))
//sampleEnd
}

```


关于在可变 Map 中使用 [`plusAssign`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/plus-assign.html)（`+=`）与 [`minusAssign`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/minus-assign.html)（`-=`）运算符的详细信息，请参见 [Map 写操作](#map-写操作) 。

## Map 写操作

[Mutable](collections-overview.html#集合类型) Map （可变 Map ）提供特定的 Map 写操作。
这些操作使你可以使用键来访问或更改 Map 值。

 Map 写操作的一些规则：

* 值可以更新。 反过来，键也永远不会改变：添加条目后，键是不变的。
* 每个键都有一个与之关联的值。也可以添加和删除整个条目。

下面是对可变 Map 中可用写操作的标准库函数的描述。

### 添加与更新条目

要将新的键值对添加到可变 Map ，请使用 [`put()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-map/put.html) 。
将新条目放入 `LinkedHashMap` （Map的默认实现）后，会添加该条目，以便在 Map 迭代时排在最后。
在 Map 类中，新元素的位置由其键顺序定义。



```kotlin
fun main() {
//sampleStart
    val numbersMap = mutableMapOf("one" to 1, "two" to 2)
    numbersMap.put("three", 3)
    println(numbersMap)
//sampleEnd
}

```


要一次添加多个条目，请使用 [`putAll()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/put-all.html) 。它的参数可以是 `Map` 或一组 `Pair` ： `Iterable` 、 `Sequence` 或 `Array` 。



```kotlin
fun main() {
//sampleStart
    val numbersMap = mutableMapOf("one" to 1, "two" to 2, "three" to 3)
    numbersMap.putAll(setOf("four" to 4, "five" to 5))
    println(numbersMap)
//sampleEnd
}

```


如果给定键已存在于 Map 中，则 `put()` 与 `putAll()` 都将覆盖值。 因此，可以使用它们来更新 Map 条目的值。



```kotlin
fun main() {
//sampleStart
    val numbersMap = mutableMapOf("one" to 1, "two" to 2)
    val previousValue = numbersMap.put("one", 11)
    println("value associated with 'one', before: $previousValue, after: ${numbersMap["one"]}")
    println(numbersMap)
//sampleEnd
}

```


还可以使用快速操作符将新条目添加到 Map 。 有两种方式：

* [`plusAssign`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/plus-assign.html) （`+=`） 操作符。
*  `[]` 操作符为 `set()` 的别名。  



```kotlin
fun main() {
//sampleStart
    val numbersMap = mutableMapOf("one" to 1, "two" to 2)
    numbersMap["three"] = 3     // 调用 numbersMap.set("three", 3)
    numbersMap += mapOf("four" to 4, "five" to 5)
    println(numbersMap)
//sampleEnd
}

```


使用 Map 中存在的键进行操作时，将覆盖相应条目的值。

### 删除条目

要从可变 Map 中删除条目，请使用 [`remove()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-map/remove.html) 函数。
调用 `remove()` 时，可以传递键或整个键值对。
如果同时指定键和值，则仅当键值都匹配时，才会删除此的元素。



```kotlin
fun main() {
//sampleStart
    val numbersMap = mutableMapOf("one" to 1, "two" to 2, "three" to 3)
    numbersMap.remove("one")
    println(numbersMap)
    numbersMap.remove("three", 4)            //不会删除任何条目
    println(numbersMap)
//sampleEnd
}

```


还可以通过键或值从可变 Map 中删除条目。
在 Map 的 `.keys` 或 `.values` 中调用 `remove()` 并提供键或值来删除条目。
在 `.values` 中调用时， `remove()` 仅删除给定值匹配到的的第一个条目。



```kotlin
fun main() {
//sampleStart
    val numbersMap = mutableMapOf("one" to 1, "two" to 2, "three" to 3, "threeAgain" to 3)
    numbersMap.keys.remove("one")
    println(numbersMap)
    numbersMap.values.remove(3)
    println(numbersMap)
//sampleEnd
}

```



 [`minusAssign`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/minus-assign.html) （`-=`） 操作符也可用于可变 Map 。



```kotlin
fun main() {
//sampleStart
    val numbersMap = mutableMapOf("one" to 1, "two" to 2, "three" to 3)
    numbersMap -= "two"
    println(numbersMap)
    numbersMap -= "five"             //不会删除任何条目
    println(numbersMap)
//sampleEnd
}

```


