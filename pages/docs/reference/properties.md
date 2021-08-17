---
type: doc
layout: reference
category: "Syntax"
title: "属性：Getters、Setters、const、lateinit"
---

# 属性

## 声明属性

Kotlin 类中的属性既可以用关键字 *var*{: .keyword } 声明为可变的，也可以用关键字 *val*{: .keyword } 声明为只读的。



```kotlin
class Address {
    var name: String = "Holmes, Sherlock"
    var street: String = "Baker"
    var city: String = "London"
    var state: String? = null
    var zip: String = "123456"
}
```


要使用一个属性，只要用名称引用它即可：



```kotlin
fun copyAddress(address: Address): Address {
    val result = Address() // Kotlin 中没有“new”关键字
    result.name = address.name // 将调用访问器
    result.street = address.street
    // ……
    return result
}
```


## Getters 与 Setters

声明一个属性的完整语法是



```kotlin
var <propertyName>[: <PropertyType>] [= <property_initializer>]
    [<getter>]
    [<setter>]
```


其初始器（initializer）、getter 和 setter 都是可选的。属性类型如果可以从初始器
（或者从其 getter 返回值，如下文所示）中推断出来，也可以省略。

例如:



```kotlin
var allByDefault: Int? // 错误：需要显式初始化器，隐含默认 getter 和 setter
var initialized = 1 // 类型 Int、默认 getter 和 setter
```


一个只读属性的语法和一个可变的属性的语法有两方面的不同：1、只读属性的用 `val`开始代替`var` 2、只读属性不允许 setter



```kotlin
val simple: Int? // 类型 Int、默认 getter、必须在构造函数中初始化
val inferredType = 1 // 类型 Int 、默认 getter
```


我们可以为属性定义自定义的访问器。如果我们定义了一个自定义的 getter，那么每次访问该属性时都会调用它
（这让我们可以实现计算出的属性）。以下是一个自定义 getter 的示例：



```kotlin
val isEmpty: Boolean
    get() = this.size == 0
```


如果我们定义了一个自定义的 setter，那么每次给属性赋值时都会调用它。一个自定义的 setter 如下所示：



```kotlin
var stringRepresentation: String
    get() = this.toString()
    set(value) {
        setDataFromString(value) // 解析字符串并赋值给其他属性
    }
```


按照惯例，setter 参数的名称是 `value`，但是如果你喜欢你可以选择一个不同的名称。

自 Kotlin 1.1 起，如果可以从 getter 推断出属性类型，则可以省略它：



```kotlin
val isEmpty get() = this.size == 0  // 具有类型 Boolean
```


如果你需要改变一个访问器的可见性或者对其注解，但是不需要改变默认的实现，
你可以定义访问器而不定义其实现:



```kotlin
var setterVisibility: String = "abc"
    private set // 此 setter 是私有的并且有默认实现

var setterWithAnnotation: Any? = null
    @Inject set // 用 Inject 注解此 setter
```


### 幕后字段

在 Kotlin 中，字段仅在需要作为属性的一部分在内存中保存其值时使用。字段不能直接声明。然而，当一个属性需要一个幕后字段时，Kotlin 会自动提供。这个幕后字段可以使用 `field` 标识符在访问器中引用：



```kotlin
var counter = 0 // 注意：这个初始器直接为幕后字段赋值
    set(value) {
        if (value >= 0) field = value
    }
```


`field` 标识符只能用在属性的访问器内。

如果属性至少一个访问器使用默认实现，或者自定义访问器通过 `field` 引用幕后字段，将会为该属性生成一个幕后字段。

例如，下面的情况下， 就没有幕后字段：



```kotlin
val isEmpty: Boolean
    get() = this.size == 0
```


### 幕后属性

如果你的需求不符合这套“隐式的幕后字段”方案，那么总可以使用 *幕后属性（backing property）*：



```kotlin
private var _table: Map<String, Int>? = null
public val table: Map<String, Int>
    get() {
        if (_table == null) {
            _table = HashMap() // 类型参数已推断出
        }
        return _table ?: throw AssertionError("Set to null by another thread")
    }
```


> **对于 JVM 平台**：通过默认 getter 和 setter 访问私有属性会被优化，
所以本例不会引入函数调用开销。


## 编译期常量

如果只读属性的值在编译期是已知的，那么可以使用 *const*{: .keyword } 修饰符将其标记为*编译期常量*。
这种属性需要满足以下要求：

  * 位于顶层或者是 [*object*{: .keyword } 声明](object-declarations.html#对象声明) 或 [*companion object*{: .keyword }](object-declarations.html#伴生对象) 的一个成员
  * 以 `String` 或原生类型值初始化
  * 没有自定义 getter

这些属性可以用在注解中：



```kotlin
const val SUBSYSTEM_DEPRECATED: String = "This subsystem is deprecated"

@Deprecated(SUBSYSTEM_DEPRECATED) fun foo() { …… }
```



## 延迟初始化属性与变量

一般地，属性声明为非空类型必须在构造函数中初始化。
然而，这经常不方便。例如：属性可以通过依赖注入来初始化，
或者在单元测试的 setup 方法中初始化。 这种情况下，你不能在构造函数内提供一个非空初始器。
但你仍然想在类体中引用该属性时避免空检测。

为处理这种情况，你可以用 `lateinit` 修饰符标记该属性：



```kotlin
public class MyTest {
    lateinit var subject: TestSubject

    @SetUp fun setup() {
        subject = TestSubject()
    }

    @Test fun test() {
        subject.method()  // 直接解引用
    }
}
```


该修饰符只能用于在类体中的属性（不是在主构造函数中声明的 `var` 属性，并且仅<!--
-->当该属性没有自定义 getter 或 setter 时），而自 Kotlin 1.2 起，也用于顶层属性与<!--
-->局部变量。该属性或变量必须为非空类型，并且不能是原生类型。

在初始化前访问一个 `lateinit` 属性会抛出一个特定异常，该异常明确标识该属性<!--
-->被访问及它没有初始化的事实。

### 检测一个 lateinit var 是否已初始化（自 1.2 起）

要检测一个 `lateinit var` 是否已经初始化过，请在[该属性的引用](reflection.html#属性引用)上使用
`.isInitialized`：



```kotlin
if (foo::bar.isInitialized) {
    println(foo.bar)
}
```


此检测仅对可词法级访问的属性可用，即声明位于同一个类型内、位于其中一个<!--
-->外围类型中或者位于相同文件的顶层的属性。

## 覆盖属性

参见[覆盖属性](classes.html#覆盖属性)

## 委托属性

最常见的一类属性就是简单地从幕后字段中读取（以及可能的写入）。
另一方面，使用自定义 getter 和 setter 可以实现属性的任何行为。
介于两者之间，属性如何工作有一些常见的模式。一些例子：惰性值、
通过键值从映射读取、访问数据库、访问时通知侦听器等等。

这些常见行为可以通过使用[_委托属性_](delegated-properties.html)实现为库。

