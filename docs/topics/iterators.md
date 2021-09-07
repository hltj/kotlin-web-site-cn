[//]: # (title: 迭代器)

对于遍历集合元素， Kotlin 标准库支持 _迭代器_ 的常用机制——对象可按顺序提供对元素的访问权限，而不会暴露集合的底层结构。
当需要逐个处理集合的所有元素（例如打印值或对其进行类似更新）时，迭代器非常有用。

[`Iterable<T>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-iterable/index.html) 接口的继承者（包括 `Set` 与 `List`）可以通过调用 [`iterator()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-iterable/iterator.html) 函数获得迭代器。
一旦获得迭代器它就指向集合的第一个元素；调用 [`next()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-iterator/next.html) 函数将返回此元素，并将迭代器指向下一个元素（如果下一个元素存在）。
一旦迭代器通过了最后一个元素，它就不能再用于检索元素；也无法重新指向到以前的任何位置。要再次遍历集合，请创建一个新的迭代器。



```kotlin
fun main() {
//sampleStart
    val numbers = listOf("one", "two", "three", "four")
    val numbersIterator = numbers.iterator()
    while (numbersIterator.hasNext()) {
        println(numbersIterator.next())
    }
//sampleEnd
}
```


遍历 `Iterable` 集合的另一种方法是众所周知的 `for` 循环。在集合中使用 `for` 循环时，将隐式获取迭代器。因此，以下代码与上面的示例等效：



```kotlin
fun main() {
//sampleStart
    val numbers = listOf("one", "two", "three", "four")
    for (item in numbers) {
        println(item)
    }
//sampleEnd
}
```


最后，有一个好用的 `forEach()` 函数，可自动迭代集合并为每个元素执行给定的代码。因此，等效的示例如下所示：



```kotlin
fun main() {
//sampleStart
    val numbers = listOf("one", "two", "three", "four")
    numbers.forEach {
        println(it)
    }
//sampleEnd
}
```


## List 迭代器

对于列表，有一个特殊的迭代器实现： [`ListIterator`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list-iterator/index.html) 它支持列表双向迭代：正向与反向。
反向迭代由 [`hasPrevious()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list-iterator/has-previous.html) 和 [`previous()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list-iterator/previous.html) 函数实现。
此外， `ListIterator` 通过 [`nextIndex()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list-iterator/next-index.html) 与 [`previousIndex()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list-iterator/previous-index.html) 函数提供有关元素索引的信息。



```kotlin
fun main() {
//sampleStart
    val numbers = listOf("one", "two", "three", "four")
    val listIterator = numbers.listIterator()
    while (listIterator.hasNext()) listIterator.next()
    println("Iterating backwards:")
    while (listIterator.hasPrevious()) {
        print("Index: ${listIterator.previousIndex()}")
        println(", value: ${listIterator.previous()}")
    }
//sampleEnd
}
```


具有双向迭代的能力意味着 `ListIterator` 在到达最后一个元素后仍可以使用。

## 可变迭代器

为了迭代可变集合，于是有了 [`MutableIterator`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-iterator/index.html) 来扩展 `Iterator` 使其具有元素删除函数 [`remove()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-iterator/remove.html) 。因此，可以在迭代时从集合中删除元素。



```kotlin
fun main() {
//sampleStart
    val numbers = mutableListOf("one", "two", "three", "four") 
    val mutableIterator = numbers.iterator()
    
    mutableIterator.next()
    mutableIterator.remove()    
    println("After removal: $numbers")
//sampleEnd
}
```


除了删除元素， [`MutableListIterator`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-list-iterator/index.html) 还可以在迭代列表时插入和替换元素。



```kotlin
fun main() {
//sampleStart
    val numbers = mutableListOf("one", "four", "four")
    val mutableListIterator = numbers.listIterator()

    mutableListIterator.next()
    mutableListIterator.add("two")
    mutableListIterator.next()
    mutableListIterator.set("three")
    println(numbers)
//sampleEnd
}
```


