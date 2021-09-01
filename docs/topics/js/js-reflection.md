[//]: # (title: JavaScript 反射)

# JavaScript 反射

目前，JavaScript 不支持完整的 Kotlin 反射 API。唯一支持的该 API 部分<!--
-->是 `::class` 语法，它允许你引用一个实例的类或者与给定类型相对应的类。
一个 `::class` 表达式的值是一个只能支持 [simpleName](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect/-k-class/simple-name.html) 和
[isInstance](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect/-k-class/is-instance.html) 成员<!--
-->的精简版 [KClass](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect/-k-class/) 实现。

除此之外，你可以使用 [KClass.js](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.js/js.html) 访问<!--
-->与 [JsClass](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.js/-js-class/index.html) 类对应的实例。
该 `JsClass` 实例本身就是对构造函数的引用。
这可以用于与期望构造函数的引用的 JS 函数进行互操作。

示例：


```kotlin
class A
class B
class C

inline fun <reified T> foo() {
    println(T::class.simpleName)
}

val a = A()
println(a::class.simpleName)  // 获取一个实例的类；输出“A”
println(B::class.simpleName)  // 获取一个类型的类；输出“B”
println(B::class.js.name)     // 输出“B”
foo<C>()                      // 输出“C”
```

