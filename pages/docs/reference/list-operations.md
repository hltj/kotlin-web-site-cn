---
type: doc
layout: reference
category: "Collections"
title: "List 相关操作"
---

# List 相关操作

[`List`](collections-overview.html#list) 是 Kotlin 标准库中最受欢迎的集合类型。对列表元素的索引访问为 List 提供了一组强大的操作。

## 按索引取元素

List 支持按索引取元素的所有常用操作： `elementAt()` 、 `first()` 、 `last()` 与[取单个元素](collection-elements.html)中列出的其他操作。
List 的特点是能通过索引访问特定元素，因此读取元素的最简单方法是按索引检索它。
这是通过 [`get()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list/get.html) 函数或简写语法 `[index]` 来传递索引参数完成的。

如果 List 长度小于指定的索引，则抛出异常。
另外，还有两个函数能避免此类异常：

* [`getOrElse()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/get-or-else.html) 提供用于计算默认值的函数，如果集合中不存在索引，则返回默认值。
* [`getOrNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/get-or-null.html) 返回 `null` 作为默认值。

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

```kotlin
fun main() {
//sampleStart
    val numbers = listOf(1, 2, 3, 4)
    println(numbers.get(0))
    println(numbers[0])
    //numbers.get(5)                         // exception!
    println(numbers.getOrNull(5))             // null
    println(numbers.getOrElse(5, {it}))        // 5
//sampleEnd
}

```
</div>

## 取列表的一部分

除了[取集合的一部分](collection-parts.html)中常用的操作， List 还提供 [`subList()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list/sub-list.html) 该函数将指定元素范围的视图作为列表返回。
因此，如果原始集合的元素发生变化，则它在先前创建的子列表中也会发生变化，反之亦然。

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

```kotlin
fun main() {
//sampleStart
    val numbers = (0..13).toList()
    println(numbers.subList(3, 6))
//sampleEnd
}

```
</div>

## 查找元素位置

### 线性查找

在任何列表中，都可以使用 [`indexOf()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/index-of.html) 或 [`lastIndexOf()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/last-index-of.html) 函数找到元素的位置。
它们返回与列表中给定参数相等的元素的第一个或最后一个位置。
如果没有这样的元素，则两个函数均返回 `-1`。

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

```kotlin
fun main() {
//sampleStart
    val numbers = listOf(1, 2, 3, 4, 2, 5)
    println(numbers.indexOf(2))
    println(numbers.lastIndexOf(2))
//sampleEnd
}

```
</div>

还有一对函数接受谓词并搜索与之匹配的元素：

* [`indexOfFirst()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/index-of-first.html) 返回与谓词匹配的*第一个元素的索引*，如果没有此类元素，则返回 `-1`。
* [`indexOfLast()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/index-of-last.html) 返回与谓词匹配的*最后一个元素的索引*，如果没有此类元素，则返回 `-1`。

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

```kotlin
fun main() {
//sampleStart
    val numbers = mutableListOf(1, 2, 3, 4)
    println(numbers.indexOfFirst { it > 2})
    println(numbers.indexOfLast { it % 2 == 1})
//sampleEnd
}

```
</div>

### 在有序列表中二分查找

还有另一种搜索列表中元素的方法——[二分查找算法](https://zh.wikipedia.org/wiki/%E4%BA%8C%E5%88%86%E6%90%9C%E5%B0%8B%E6%BC%94%E7%AE%97%E6%B3%95)。
它的工作速度明显快于其他内置搜索功能，但*要求该列表按照一定的顺序*（自然排序或函数参数中提供的另一种排序）按升序[排序过](collection-ordering.html)。
否则，结果是不确定的。

要搜索已排序列表中的元素，请调用 [`binarySearch()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/binary-search.html) 函数，并将该值作为参数传递。
如果存在这样的元素，则函数返回其索引；否则，将返回 `(-insertionPoint - 1)`，其中 `insertionPoint` 为应插入此元素的索引，以便列表保持排序。
如果有多个具有给定值的元素，搜索则可以返回其任何索引。

还可以指定要搜索的索引区间：在这种情况下，该函数仅在两个提供的索引之间搜索。

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

```kotlin
fun main() {
//sampleStart
    val numbers = mutableListOf("one", "two", "three", "four")
    numbers.sort()
    println(numbers)
    println(numbers.binarySearch("two"))  // 3
    println(numbers.binarySearch("z")) // -5
    println(numbers.binarySearch("two", 0, 2))  // -3
//sampleEnd
}

```
</div>

#### Comparator 二分搜索

如果列表元素不是 `Comparable`，则应提供一个用于二分搜索的 [`Comparator`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-comparator.html)。
该列表必须根据此 `Comparator` 以升序排序。来看一个例子：

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

```kotlin
data class Product(val name: String, val price: Double)

fun main() {
//sampleStart
    val productList = listOf(
        Product("WebStorm", 49.0),
        Product("AppCode", 99.0),
        Product("DotTrace", 129.0),
        Product("ReSharper", 149.0))

    println(productList.binarySearch(Product("AppCode", 99.0), compareBy<Product> { it.price }.thenBy { it.name }))
//sampleEnd
}

```
</div>

这是一个不可`排序`的 `Product` 实例列表，以及一个定义排序的 `Comparator`：如果 `p1` 的价格小于 `p2` 的价格，则产品 `p1` 在产品 `p2` 之前。
因此，按照此顺序对列表进行升序排序后，使用 `binarySearch()` 查找指定的 `Product`的索引。

当列表使用与自然排序不同的顺序时（例如，对 `String` 元素不区分大小写的顺序），自定义 Comparator 也很方便。

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

```kotlin
fun main() {
//sampleStart
    val colors = listOf("Blue", "green", "ORANGE", "Red", "yellow")
    println(colors.binarySearch("RED", String.CASE_INSENSITIVE_ORDER)) // 3
//sampleEnd
}

```
</div>

#### 比较函数二分搜索

使用 _比较_ 函数的二分搜索无需提供明确的搜索值即可查找元素。
取而代之的是，它使用一个比较函数将元素映射到 `Int` 值，并搜索函数返回 0 的元素。
该列表必须根据提供的函数以升序排序；换句话说，比较的返回值必须从一个列表元素增长到下一个列表元素。

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

```kotlin
import kotlin.math.sign
//sampleStart
data class Product(val name: String, val price: Double)

fun priceComparison(product: Product, price: Double) = sign(product.price - price).toInt()

fun main() {
    val productList = listOf(
        Product("WebStorm", 49.0),
        Product("AppCode", 99.0),
        Product("DotTrace", 129.0),
        Product("ReSharper", 149.0))

    println(productList.binarySearch { priceComparison(it, 99.0) })
}
//sampleEnd
```
</div>

Comparator 与比较函数二分搜索都可以针对列表区间执行。

## List 写操作

除了[集合写操作](collection-write.html)中描述的集合修改操作之外，[可变](collections-overview.html#集合类型)列表还支持特定的写操作。
这些操作使用索引来访问元素以扩展列表修改功能。

### 添加

要将元素添加到列表中的特定位置，请使用 [`add()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-list/add.html) 或 [`addAll()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/add-all.html) 并提供元素插入的位置作为附加参数。
位置之后的所有元素都将向右移动。

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

```kotlin
fun main() {
//sampleStart
    val numbers = mutableListOf("one", "five", "six")
    numbers.add(1, "two")
    numbers.addAll(2, listOf("three", "four"))
    println(numbers)
//sampleEnd
}

```
</div>

### 更新

列表还提供了在指定位置替换元素的函数——[`set()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-list/set.html) 及其操作符形式 `[]`。`set()` 不会更改其他元素的索引。

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

```kotlin
fun main() {
//sampleStart
    val numbers = mutableListOf("one", "five", "three")
    numbers[1] =  "two"
    println(numbers)
//sampleEnd
}

```
</div>

[`fill()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/fill.html) 简单地将所有集合元素的值替换为指定值。

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

```kotlin
fun main() {
//sampleStart
    val numbers = mutableListOf(1, 2, 3, 4)
    numbers.fill(3)
    println(numbers)
//sampleEnd
}

```
</div>


### 删除

要从列表中删除指定位置的元素，请使用 [`removeAt()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-list/remove-at.html) 函数，并将位置作为参数。
在元素被删除之后出现的所有元素索引将减 1。

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

```kotlin
fun main() {
//sampleStart
    val numbers = mutableListOf(1, 2, 3, 4, 3)    
    numbers.removeAt(1)
    println(numbers)
//sampleEnd
}

```
</div>

For removing the first and the last element, there are handy shortcuts [`removeFirst()`](/api/latest/jvm/stdlib/kotlin.collections/-mutable-list/remove-first.html)
and [`removeLast()`](/api/latest/jvm/stdlib/kotlin.collections/-mutable-list/remove-last.html). Note that on empty lists,
they throw an exception. To receive `null` instead, use [`removeFirstOrNull()`](/api/latest/jvm/stdlib/kotlin.collections/-mutable-list/remove-first-or-null.html)
and [`removeLastOrNull()`](/api/latest/jvm/stdlib/kotlin.collections/-mutable-list/remove-last-or-null.html)

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

```kotlin
fun main() {
//sampleStart
    val numbers = mutableListOf(1, 2, 3, 4, 3)    
    numbers.removeFirst()
    numbers.removeLast()
    println(numbers)
    
    val empty = mutableListOf<Int>()
    // empty.removeFirst() // NoSuchElementException: List is empty.
    empty.removeFirstOrNull() //null
//sampleEnd
}

```
</div>


### 排序

在[集合排序](collection-ordering.html)中，描述了按特定顺序检索集合元素的操作。
对于可变列表，标准库中提供了类似的扩展函数，这些扩展函数可以执行相同的排序操作。
将此类操作应用于列表实例时，它将更改指定实例中元素的顺序。

就地排序函数的名称与应用于只读列表的函数的名称相似，但没有 `ed/d` 后缀：

*  `sort*` 在所有排序函数的名称中代替 `sorted*`：[`sort()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sort.html)、[`sortDescending()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sort-descending.html)、[`sortBy()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sort-by.html) 等等。
* [`shuffle()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/shuffle.html) 代替 `shuffled()`。
* [`reverse()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/reverse.html) 代替 `reversed()`。

[`asReversed()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/as-reversed.html) 在可变列表上调用会返回另一个可变列表，该列表是原始列表的反向视图。在该视图中的更改将反映在原始列表中。
以下示例展示了可变列表的排序函数：


<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

```kotlin
fun main() {
//sampleStart
    val numbers = mutableListOf("one", "two", "three", "four")

    numbers.sort()
    println("Sort into ascending: $numbers")
    numbers.sortDescending()
    println("Sort into descending: $numbers")

    numbers.sortBy { it.length }
    println("Sort into ascending by length: $numbers")
    numbers.sortByDescending { it.last() }
    println("Sort into descending by the last letter: $numbers")
    
    numbers.sortWith(compareBy<String> { it.length }.thenBy { it })
    println("Sort by Comparator: $numbers")

    numbers.shuffle()
    println("Shuffle: $numbers")

    numbers.reverse()
    println("Reverse: $numbers")
//sampleEnd
}

```
</div>



