---
type: doc
layout: reference
category: "Syntax"
title: "委托"
---

# 委托

## 属性委托

属性委托在单独一页中讲：[属性委托](delegated-properties.html)。

## 由委托实现

[委托模式](https://zh.wikipedia.org/wiki/%E5%A7%94%E6%89%98%E6%A8%A1%E5%BC%8F)已经证明是实现继承的一个很好的替代方式，
而 Kotlin 可以零样板代码地原生支持它。
`Derived` 类可以通过将其所有公有成员都委托给指定对象来实现一个接口 `Base`：

<div class="sample" markdown="1" theme="idea">

```kotlin
interface Base {
    fun print()
}

class BaseImpl(val x: Int) : Base {
    override fun print() { print(x) }
}

class Derived(b: Base) : Base by b

fun main() {
    val b = BaseImpl(10)
    Derived(b).print()
}
```

</div>

`Derived` 的超类型列表中的 *by*{: .keyword }-子句表示 `b` 将会在 `Derived` 中内部存储，
并且编译器将生成转发给 `b` 的所有 `Base` 的方法。

### 覆盖由委托实现的接口成员

[覆盖](classes.html#覆盖方法)符合预期：编译器会使用 `override`
覆盖的实现而不是委托对象中的。如果将 `override fun printMessage() { print("abc") }` 添加到
`Derived`，那么当调用 `printMessage` 时程序会输出“abc”而不是“10”：

<div class="sample" markdown="1" theme="idea">

```kotlin
interface Base {
    fun printMessage()
    fun printMessageLine()
}

class BaseImpl(val x: Int) : Base {
    override fun printMessage() { print(x) }
    override fun printMessageLine() { println(x) }
}

class Derived(b: Base) : Base by b {
    override fun printMessage() { print("abc") }
}

fun main() {
    val b = BaseImpl(10)
    Derived(b).printMessage()
    Derived(b).printMessageLine()
}
```

</div>

但请注意，以这种方式重写的成员不会在委托对象的成员中调用
，委托对象的成员只能访问其自身对接口成员实现：

<div class="sample" markdown="1" theme="idea">

```kotlin
interface Base {
    val message: String
    fun print()
}

class BaseImpl(val x: Int) : Base {
    override val message = "BaseImpl: x = $x"
    override fun print() { println(message) }
}

class Derived(b: Base) : Base by b {
    // 在 b 的 `print` 实现中不会访问到这个属性
    override val message = "Message of Derived"
}

fun main() {
    val b = BaseImpl(10)
    val derived = Derived(b)
    derived.print()
    println(derived.message)
}
```

</div>
