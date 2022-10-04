[//]: # (title: 习惯用法)

一些在 Kotlin 中广泛使用的语法习惯，如果你有更喜欢的语法习惯或者风格，建一个 pull request 贡献给我们吧！

### 创建 DTO（POJO/POCO）

```kotlin
data class Customer(val name: String, val email: String)
```

会为 `Customer` 类提供以下功能：

* 所有属性的 getter （对于 `var` 定义的还有 setter）
* `equals()`
* `hashCode()`
* `toString()`
* `copy()`
* 所有属性的 `component1()`、 `component2()`……等等（参见[数据类](data-classes.md)）

### 函数的默认参数

```kotlin
fun foo(a: Int = 0, b: String = "") { …… }
```

### 过滤 list

```kotlin
val positives = list.filter { x -> x > 0 }
```

或者可以更短:

```kotlin
val positives = list.filter { it > 0 }
```

了解 [Java 与 Kotlin 过滤](java-to-kotlin-collections-guide.md#过滤元素)的区别。

### 检测元素是否存在于集合中

```kotlin
if ("john@example.com" in emailsList) { …… }

if ("jane@example.com" !in emailsList) { …… }
```

### 字符串内插

```kotlin
println("Name $name")
```

了解 [Java 与 Kotlin 字符串连接](java-to-kotlin-idioms-strings.md#字符串连接)的区别。

### 类型判断

```kotlin
when (x) {
    is Foo -> ……
    is Bar -> ……
    else   -> ……
}
```

## 只读 list

```kotlin
val list = listOf("a", "b", "c")
```
## 只读 map

```kotlin
val map = mapOf("a" to 1, "b" to 2, "c" to 3)
```

## 访问 map 条目

```kotlin
println(map["key"])
map["key"] = value
```

## 遍历 map 或者 pair 型 list

```kotlin
for ((k, v) in map) {
    println("$k -> $v")
}
```

`k` 与 `v` 可以是任何适宜的名称，例如 `name` 与 `age`。

### 区间迭代

```kotlin
for (i in 1..100) { …… }  // 闭区间：包含 100
for (i in 1 until 100) { …… } // 半开区间：不包含 100
for (x in 2..10 step 2) { …… }
for (x in 10 downTo 1) { …… }
(1..10).forEach { …… }
```

## 延迟属性

```kotlin
val p: String by lazy { // the value is computed only on first access
    // 计算该字符串
}
```

## 扩展函数

```kotlin
fun String.spaceToCamelCase() { …… }

"Convert this to camelcase".spaceToCamelCase()
```

## 创建单例

```kotlin
object Resource {
    val name = "Name"
}
```

## 实例化一个抽象类

```kotlin
abstract class MyAbstractClass {
    abstract fun doSomething()
    abstract fun sleep()
}

fun main() {
    val myObject = object : MyAbstractClass() {
        override fun doSomething() {
            // ……
        }

        override fun sleep() { // ……
        }
    }
    myObject.doSomething()
}
```

## if-not-null 缩写

```kotlin
val files = File("Test").listFiles()

println(files?.size) // 如果 files 不是 null，那么输出其大小（size）
```

## if-not-null-else 缩写

```kotlin
val files = File("Test").listFiles()

println(files?.size ?: "empty") // 如果 files 为 null，那么输出“empty”

// To calculate the fallback value in a code block, use `run`
val filesSize = files?.size ?: run { 
    return someSize 
}
println(filesSize)
```

## if null 执行一个语句

```kotlin
val values = ……
val email = values["email"] ?: throw IllegalStateException("Email is missing!")
```

## 在可能会空的集合中取第一元素

```kotlin
val emails = …… // 可能会是空集合
val mainEmail = emails.firstOrNull() ?: ""
```

了解 [Java 与 Kotlin 获取第一元素](java-to-kotlin-collections-guide.md#获取可能会空的集合的首末元素)的差别。

## if not null 执行代码

```kotlin
val value = ……

value?.let {
…… // 代码会执行到此处, 假如data不为null
}
```

## 映射可空值（如果非空的话）

```kotlin
val value = ……

val mapped = value?.let { transformValue(it) } ?: defaultValue 
// 如果该值或其转换结果为空，那么返回 defaultValue。
```

## 返回 when 表达式

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

### try-catch 表达式

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

### if 表达式

```kotlin
val y = if (x == 1) {
    "one"
} else if (x == 2) {
    "two"
} else {
    "other"
}
```

### 返回类型为 Unit 的方法的构建器风格用法

```kotlin
fun arrayOfMinusOnes(size: Int): IntArray {
    return IntArray(size).apply { fill(-1) }
}
```

## 单表达式函数

```kotlin
fun theAnswer() = 42
```

等价于

```kotlin
fun theAnswer(): Int {
    return 42
}
```

单表达式函数与其它惯用法一起使用能简化代码，例如和 `when` 表达式一起使用：

```kotlin
fun transform(color: String): Int = when (color) {
    "Red" -> 0
    "Green" -> 1
    "Blue" -> 2
    else -> throw IllegalArgumentException("Invalid color param value")
}
```

## 对一个对象实例调用多个方法 （with）

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

## 配置对象的属性（apply）

```kotlin
val myRectangle = Rectangle().apply {
    length = 4
    breadth = 5
    color = 0xFAFAFA
}
```

这对于配置未出现在对象构造函数中的属性非常有用。

## Java 7 的 try-with-resources

```kotlin
val stream = Files.newInputStream(Paths.get("/some/file.txt"))
stream.buffered().reader().use { reader ->
    println(reader.readText())
}
```

## 需要泛型信息的泛型函数

```kotlin
//  public final class Gson {
//     ……
//     public <T> T fromJson(JsonElement json, Class<T> classOfT) throws JsonSyntaxException {
//     ……

inline fun <reified T: Any> Gson.fromJson(json: JsonElement): T = this.fromJson(json, T::class.java)
```

## 可空布尔

```kotlin
val b: Boolean? = ……
if (b == true) {
    ……
} else {
    // `b` 是 false 或者 null
}
```

## 交换两个变量

```kotlin
var a = 1
var b = 2
a = b.also { b = a }
```

### 将代码标记为不完整（TODO）
 
Kotlin 的标准库有一个 `TODO()` 函数，该函数总是抛出一个 `NotImplementedError`。
其返回类型为 `Nothing`，因此无论预期类型是什么都可以使用它。
还有一个接受原因参数的重载：

```kotlin
fun calcTaxes(): BigDecimal = TODO("Waiting for feedback from accounting")
```

IntelliJ IDEA 的 kotlin 插件理解 `TODO()` 的语言，并且会自动在 TODO 工具窗口中添加代码指示。 

## 下一步做什么？

* 使用地道 Kotlin 风格求解 [Advent of Code 谜题](advent-of-code.md)
* 了解如何[在 Java 与 Kotlin 中处理字符串的典型任务](java-to-kotlin-idioms-strings.md)
* Learn how to perform [typical tasks with collections in Java and Kotlin](java-to-kotlin-collections-guide.md).
* Learn how to [handle nullability in Java and Kotlin](java-to-kotlin-nullability-guide.md).
