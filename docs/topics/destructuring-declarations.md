[//]: # (title: 解构声明)

有时把一个对象*解构*成很多变量会很方便，例如:

```kotlin
val (name, age) = person
```

这种语法称为*解构声明*。一个解构声明同时创建多个变量。
声明了两个新变量： `name` 和 `age`，并且可以独立使用它们：

 ```kotlin
println(name)
println(age)
```

一个解构声明会被编译成以下代码：

```kotlin
val name = person.component1()
val age = person.component2()
```

其中的 `component1()` 和 `component2()` 函数是在 Kotlin 中广泛使用的*约定原则*的另一个示例。
（参见像 `+` 和 `*`、`for`-循环作为示例）。
任何表达式都可以出现在解构声明的右侧，只要可以对它调用所需数量的 component
函数即可。 当然，可以有 `component3()` 和 `component4()` 等等。

> `componentN()` 函数需要用 `operator` 关键字标记，以允许在解构声明中使用它们 
> 。
>
{type="note"}

解构声明也可以用在 `for`-循环中：

```kotlin
for ((a, b) in collection) { …… }
```

变量 `a` 和 `b` 的值取自对集合中的元素上调用 `component1()` 和 `component2()` 的返回值。 

## 例：从函数中返回两个值
 
让我们假设我们需要从一个函数返回两个东西。例如，一个结果对象和一个某种状态。
在 Kotlin 中一个简洁的实现方式是声明一个[数据类](data-classes.md) 并返回其实例：

```kotlin
data class Result(val result: Int, val status: Status)
fun function(……): Result {
    // 各种计算

    return Result(result, status)
}

// 现在，使用该函数：
val (result, status) = function(……)
```

因为数据类自动声明 `componentN()` 函数，所以这里可以用解构声明。

> 我们也可以使用标准类 `Pair` 并且让 `function()` 返回 `Pair<Int, Status>`，
> 但是让数据合理命名通常更好。
>
{type="note"}

## 例：解构声明与映射

可能遍历一个映射（map）最好的方式就是这样：

```kotlin
for ((key, value) in map) {
   // 使用该 key、value 做些事情
}
```

为使其能用，我们应该

* 通过提供一个 `iterator()` 函数将映射表示为一个值的序列。
* 通过提供函数 `component1()` 和 `component2()` 来将每个元素呈现为一对。
  
当然事实上，标准库提供了这样的扩展：

```kotlin
operator fun <K, V> Map<K, V>.iterator(): Iterator<Map.Entry<K, V>> = entrySet().iterator()
operator fun <K, V> Map.Entry<K, V>.component1() = getKey()
operator fun <K, V> Map.Entry<K, V>.component2() = getValue()
```

因此你可以在 `for`-循环中对映射（以及数据类实例的集合等）自由使用解构声明。

## 下划线用于未使用的变量

如果在解构声明中你不需要某个变量，那么可以用下划线取代其名称：

```kotlin
val (_, status) = getResult()
```

对于以这种方式跳过的组件，不会调用相应的 `componentN()` 操作符函数。

## 在 lambda 表达式中解构

你可以对 lambda 表达式参数使用解构声明语法。
如果 lambda 表达式具有 `Pair` 类型（或者 `Map.Entry` 或任何其他具有相应 `componentN` 
函数的类型）的参数，那么可以通过将它们放在括号中来引入多个新参数来取代单个新参数：   

```kotlin
map.mapValues { entry -> "${entry.value}!" }
map.mapValues { (key, value) -> "$value!" }
```

注意声明两个参数和声明一个解构对来取代单个参数之间的区别：  

```kotlin
{ a //-> …… } // 一个参数
{ a, b //-> …… } // 两个参数
{ (a, b) //-> …… } // 一个解构对
{ (a, b), c //-> …… } // 一个解构对以及其他参数
```

如果解构的参数中的一个组件未使用，那么可以将其替换为下划线，以避免编造其名称：

```kotlin
map.mapValues { (_, value) -> "$value!" }
```

你可以指定整个解构的参数的类型或者分别指定特定组件的类型：

```kotlin
map.mapValues { (_, value): Map.Entry<Int, String> -> "$value!" }

map.mapValues { (_, value: String) -> "$value!" }
```

