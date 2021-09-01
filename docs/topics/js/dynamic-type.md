[//]: # (title: 动态类型)

> 在面向 JVM 平台的代码中不支持动态类型。
>
{type="note"}

Being a statically typed language, Kotlin still has to interoperate with untyped or loosely typed environments,
such as the JavaScript ecosystem. To facilitate these use cases, the `dynamic` type is available in the language:

```kotlin
val dyn: dynamic = ……
```

`dynamic` 类型基本上关闭了 Kotlin 的类型检测系统：

- A value of the `dynamic` type can be assigned to any variable or passed anywhere as a parameter.
- Any value can be assigned to a variable of the `dynamic` type or passed to a function that takes `dynamic` as a parameter.
- `null`-checks are disabled for the `dynamic` type values.

The most peculiar feature of `dynamic` is that we are allowed to call **any** property or function with any parameters
on a `dynamic` variable:

```kotlin
dyn.whatever(1, "foo", dyn) // “whatever”在任何地方都没有定义
dyn.whatever(*arrayOf(1, 2, 3))
```

On the JavaScript platform 这段代码会按照原样编译：在生成的 JavaScript 代码中，Kotlin中的 `dyn.whatever(1)` 变为 `dyn.whatever(1)`
。

当在 `dynamic` 类型的值上调用 Kotlin 写的函数时，请记住由
Kotlin 到 JavaScript 编译器执行的名字修饰。你可能需要使用 [@JsName 注解](js-to-kotlin-interop.md#jsname-注解)
为需要调用的函数分配明确定义的名称。

动态调用总是返回 `dynamic` 作为结果，因此可以自由链式调用：

```kotlin
dyn.foo().bar.baz()
```

当把一个 lambda 表达式传给一个动态调用时，它的所有参数默认都是 `dynamic` 类型的：

```kotlin
dyn.foo {
    x -> x.bar() // x 是 dynamic
}
```

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
