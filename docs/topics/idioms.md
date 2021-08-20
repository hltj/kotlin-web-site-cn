---
type: doc
layout: reference
category: "Basics"
title: "习惯用法"
---

# 习惯用法

一些在 Kotlin 中广泛使用的语法习惯，如果你有更喜欢的语法习惯或者风格，建一个 pull request 贡献给我们吧！

### 创建 DTOs（POJOs/POCOs）

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
data class Customer(val name: String, val email: String)
```
</div>

会为 `Customer` 类提供以下功能：

* 所有属性的 getters （对于 *var*{: .keyword } 定义的还有 setters）
* `equals()`
* `hashCode()`
* `toString()`
* `copy()`
* 所有属性的 `component1()`、 `component2()`……等等（参见[数据类](data-classes.html)）


### 函数的默认参数

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
fun foo(a: Int = 0, b: String = "") { …… }
```
</div>

### 过滤 list

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
val positives = list.filter { x -> x > 0 }
```
</div>

或者可以更短:

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
val positives = list.filter { it > 0 }
```
</div>

### 检测元素是否存在于集合中

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
if ("john@example.com" in emailsList) { …… }

if ("jane@example.com" !in emailsList) { …… }
```
</div>

### 字符串内插

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
println("Name $name")
```
</div>

### 类型判断

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
when (x) {
    is Foo //-> ……
    is Bar //-> ……
    else   //-> ……
}
```
</div>

### 遍历 map/pair型list

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
for ((k, v) in map) {
    println("$k -> $v")
}
```
</div>

`k`、`v` 可以改成任意名字。

### 使用区间

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
for (i in 1..100) { …… }  // 闭区间：包含 100
for (i in 1 until 100) { …… } // 半开区间：不包含 100
for (x in 2..10 step 2) { …… }
for (x in 10 downTo 1) { …… }
if (x in 1..10) { …… }
```
</div>

### 只读 list

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
val list = listOf("a", "b", "c")
```
</div>

### 只读 map

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
val map = mapOf("a" to 1, "b" to 2, "c" to 3)
```
</div>

### 访问 map

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
println(map["key"])
map["key"] = value
```
</div>

### 延迟属性

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
val p: String by lazy {
    // 计算该字符串
}
```
</div>

### 扩展函数

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
fun String.spaceToCamelCase() { …… }

"Convert this to camelcase".spaceToCamelCase()
```
</div>

### 创建单例

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
object Resource {
    val name = "Name"
}
```
</div>

### Instantiate an abstract class

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
abstract class MyAbstractClass {
    abstract fun doSomething()
    abstract fun sleep()
}

fun main() {
    val myObject = object : MyAbstractClass() {
        override fun doSomething() {
            // ...
        }

        override fun sleep() { // ...
        }
    }
    myObject.doSomething()
}
```
</div>

### If not null 缩写

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
val files = File("Test").listFiles()

println(files?.size)
```
</div>

### If not null and else 缩写

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
val files = File("Test").listFiles()

println(files?.size ?: "empty")
```
</div>

### if null 执行一个语句

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
val values = ……
val email = values["email"] ?: throw IllegalStateException("Email is missing!")
```
</div>

### 在可能会空的集合中取第一元素

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
val emails = …… // 可能会是空集合
val mainEmail = emails.firstOrNull() ?: ""
```
</div>

### if not null 执行代码

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
val value = ……

value?.let {
…… // 代码会执行到此处, 假如data不为null
}
```
</div>

### 映射可空值（如果非空的话）

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
val value = ……

val mapped = value?.let { transformValue(it) } ?: defaultValue 
// 如果该值或其转换结果为空，那么返回 defaultValue。
```
</div>

### 返回 when 表达式

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
fun transform(color: String): Int {
    return when (color) {
        "Red" -> 0
        "Green" -> 1
        "Blue" -> 2
        else -> throw IllegalArgumentException("Invalid color param value")
    }
}
```
</div>

### “try/catch”表达式

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
fun test() {
    val result = try {
        count()
    } catch (e: ArithmeticException) {
        throw IllegalStateException(e)
    }

    // 使用 result
}
```
</div>

### “if”表达式

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
fun foo(param: Int) {
    val result = if (param == 1) {
        "one"
    } else if (param == 2) {
        "two"
    } else {
        "three"
    }
}
```
</div>

### 返回类型为 `Unit` 的方法的 Builder 风格用法

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
fun arrayOfMinusOnes(size: Int): IntArray {
    return IntArray(size).apply { fill(-1) }
}
```
</div>


### 单表达式函数

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
fun theAnswer() = 42
```
</div>

等价于

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
fun theAnswer(): Int {
    return 42
}
```
</div>

单表达式函数与其它惯用法一起使用能简化代码，例如和 *when*{: .keyword } 表达式一起使用：

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
fun transform(color: String): Int = when (color) {
    "Red" -> 0
    "Green" -> 1
    "Blue" -> 2
    else -> throw IllegalArgumentException("Invalid color param value")
}
```
</div>

### 对一个对象实例调用多个方法 （`with`）

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
class Turtle {
    fun penDown()
    fun penUp()
    fun turn(degrees: Double)
    fun forward(pixels: Double)
}

val myTurtle = Turtle()
with(myTurtle) { // 画一个 100 像素的正方形
penDown()
for (i in 1..4) {
forward(100.0)
turn(90.0)
}
penUp()
}
```
</div>

### 配置对象的属性（`apply`）
<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
val myRectangle = Rectangle().apply {
    length = 4
    breadth = 5
    color = 0xFAFAFA
}
```
</div>

这对于配置未出现在对象构造函数中的属性非常有用。

### Java 7 的 try with resources

<div class="sample" markdown="1" theme="idea" data-highlight-only auto-indent="false">
```kotlin
val stream = Files.newInputStream(Paths.get("/some/file.txt"))
stream.buffered().reader().use { reader ->
    println(reader.readText())
}
```
</div>

### 对于需要泛型信息的泛型函数的适宜形式

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
//  public final class Gson {
//     ……
//     public <T> T fromJson(JsonElement json, Class<T> classOfT) throws JsonSyntaxException {
//     ……

inline fun <reified T: Any> Gson.fromJson(json: JsonElement): T = this.fromJson(json, T::class.java)
```
</div>

### 使用可空布尔

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
val b: Boolean? = ……
if (b == true) {
    ……
} else {
    // `b` 是 false 或者 null
}
```
</div>

### 交换两个变量

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
var a = 1
var b = 2
a = b.also { b = a }
```
</div>

### TODO()：将代码标记为不完整

Kotlin 的标准库有一个 `TODO()` 函数，该函数总是抛出一个 `NotImplementedError`。
其返回类型为 `Nothing`，因此无论预期类型是什么都可以使用它。
还有一个接受原因参数的重载：
```kotlin
fun calcTaxes(): BigDecimal = TODO("Waiting for feedback from accounting")
```

IntelliJ IDEA 的 kotlin 插件理解 `TODO()` 的语言，并且会自动在 TODO 工具窗口中添加代码指示。 

