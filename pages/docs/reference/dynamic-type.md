---
type: doc
layout: reference
category: "Syntax"
title: "动态类型"
---

# 动态类型

> 在面向 JVM 平台的代码中不支持动态类型。
{:.note}

Kotlin 是一种静态类型的语言，这使得它不同于动态类型的 JavaScript。为了方便与 JavaScript 代码的互操作，Kotlin/JS 提供了 `dynamic` 类型：

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
val dyn: dynamic = ……
```
</div>

`dynamic` 类型基本上关闭了 Kotlin 的类型检测系统：

  - `dynamic` 值可以赋值给任何类型的变量，也可以作为参数传递到任何地方。
  - `dynamic` 变量可以具有任何类型的值。
  - 带有 `dynamic` 参数的函数可以带有任何类型的参数。
  - 对于 `dynamic` 类型的值，将禁用 `null` 检查。

在 `dynamic` 变量上，可以使用任何参数调用 **任何** 属性或函数：

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
dyn.whatever(1, "foo", dyn) // “whatever”在任何地方都没有定义
dyn.whatever(*arrayOf(1, 2, 3))
```
</div>

这段代码会按照原样编译：在生成的 JavaScript 代码中，Kotlin中的 `dyn.whatever(1)` 变为 `dyn.whatever(1)`
。

当在 `dynamic` 类型的值上调用 Kotlin 写的函数时，请记住由
Kotlin 到 JavaScript 编译器执行的名字修饰。你可能需要使用 [@JsName 注解](js-to-kotlin-interop.html#jsname-注解) 或者 [@JsExport 注解](js-to-kotlin-interop.html#jsexport-annotation)为要调用的函数分配明确定义的名称。

动态调用总是返回 `dynamic` 作为结果，这意味着可以自由链式调用：

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
dyn.foo().bar.baz()
```
</div>

当我们把一个 lambda 表达式传给一个动态调用时，它的所有参数默认都是 `dynamic` 类型的：

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
dyn.foo {
    x -> x.bar() // x 是 dynamic
}
```
</div>

使用 `dynamic` 类型值的表达式会按照原样转换为 JavaScript，并且不使用 Kotlin 运算符约定。
支持以下运算符：

* 二元：`+`、 `-`、 `*`、 `/`、 `%`、 `>`、 `<`、 `>=`、 `<=`、 `==`、 `!=`、 `===`、 `!==`、 `&&`、 `||`
* 一元
    * 前置：`-`、 `+`、 `!`
    * 前置及后置：`++`、 `--`
* 赋值：`+=`、 `-=`、 `*=`、 `/=`、 `%=`
* 索引访问：
    * 读：`d[a]`，多于一个参数会出错
    * 写：`d[a1] = a2`，`[]` 中有多于一个参数会出错

`in`、 `!in` 以及 `..` 操作对于 `dynamic` 类型的值是禁用的。

更多技术说明请参见[规范文档](https://github.com/JetBrains/kotlin/blob/master/spec-docs/dynamic-types.md)。
