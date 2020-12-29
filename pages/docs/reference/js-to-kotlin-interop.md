---
type: doc
layout: reference
category: "JavaScript"
title: "JavaScript 中调用 Kotlin"
---

# JavaScript 中调用 Kotlin

根据所选的 [JavaScript 模块](js-modules.html)系统，Kotlin/JS 编译器会生成不同的输出。当然通常 Kotlin 编译器生成正常的 JavaScript 类，可以在 JavaScript 代码中自由地使用的函数和属性。不过，应该记住一些微妙的事情。

## 在 `plain` 模式中用独立的 JavaScript 隔离声明
如果将模块种类明确设置为 `plain`, 为了防止损坏全局对象，Kotlin 创建一个包含当前模块中所有 Kotlin 声明的对象。这意味着对于一个模块 `myModule`，所有的声明都可以通过 `myModule` 对象在 JavaScript 中使用。例如：

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
fun foo() = "Hello"
```
</div>

可以在 JavaScript 中这样调用：

<div class="sample" markdown="1" theme="idea" data-highlight-only>
``` javascript
alert(myModule.foo());
```
</div>

将 Kotlin 模块编译为 JavaScript 模块（例如 UMD（这是 `browser` 与 `nodejs` 目标的默认设置）、CommonJS 或 AMD）时，此方法不适用。在这种情况下，声明将以选择的 JavaScript 模块系统指定的格式暴露。例如，当使用 UMD 或 CommonJS 时，调用处可能如下所示：

<div class="sample" markdown="1" theme="idea" data-highlight-only>
``` javascript
alert(require('myModule').foo());
```
</div>

查看有关 [JavaScript 模块](js-modules.html)的文章，以获取有关 JavaScript 模块系统专题的更多信息。

## 包结构

Kotlin 将其包结构暴露给 JavaScript，因此除非你在根包中定义声明，
否则必须在 JavaScript 中使用完整限定名。例如：

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
package my.qualified.packagename

fun foo() = "Hello"
```
</div>

例如，当使用 UMD 或 CommonJS 时，调用处可能如下所示：

<div class="sample" markdown="1" theme="idea" data-highlight-only>
``` javascript
alert(require('myModule').my.qualified.packagename.foo())
```
</div>

或者，在使用 `plain` 格式作为模块系统设置的情况下：

<div class="sample" markdown="1" theme="idea" data-highlight-only>
``` javascript
alert(myModule.my.qualified.packagename.foo());
```
</div>


### `@JsName` 注解

在某些情况下（例如为了支持重载），Kotlin 编译器会修饰（mangle） JavaScript 代码中生成的函数和属性<!--
-->的名称。要控制生成的名称，可以使用 `@JsName` 注解：

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
// 模块“kjs”
class Person(val name: String) {
    fun hello() {
        println("Hello $name!")
    }

    @JsName("helloWithGreeting")
    fun hello(greeting: String) {
        println("$greeting $name!")
    }
}
```
</div>

现在，你可以通过以下方式在 JavaScript 中使用这个类：

<div class="sample" markdown="1" theme="idea" data-highlight-only>
``` javascript
// 如有必要，根据所选模块系统导入“kjs”
var person = new kjs.Person("Dmitry");   // 引用到模块“kjs”
person.hello();                          // 输出“Hello Dmitry!”
person.helloWithGreeting("Servus");      // 输出“Servus Dmitry!”
```
</div>

如果我们没有指定 `@JsName` 注解，相应函数的名称会包含<!--
-->从函数签名计算而来的后缀，例如 `hello_61zpoe$`。

请注意，在某些情况下，Kotlin 编译器不应用修饰：
- `external` 声明不会被修饰
- 从 `external` 类继承的非 `external` 类中的任何重写函数都不会被修饰。


`@JsName` 的参数需要是一个常量字符串字面值，该字面值是一个有效的标识符。
任何尝试将非标识符字符串传递给 `@JsName` 时，编译器都会报错。
以下示例会产生编译期错误：

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
@JsName("new C()")   // 此处出错
external fun newC()
```
</div>

### `@JsExport` 注解
> `@JsExport` 注解当前标记为实验性的。其设计可能会在将来的版本中更改。
{:.note} 

通过将 `@JsExport` 注解应用于顶级声明（如类或函数），可以从 JavaScript 使用 Kotlin 声明。注解会导出所有嵌套声明，并使用 Kotlin 中给出的名称。也可以使用 `@file:JsExport` 将其应用于文件级。

要解决导出中的歧义（例如，具有相同名称的函数的重载），可以将 `@JsExport` 批注与 `@JsName` 一起使用，以指定生成与导出函数的名称。

`@JsExport` 注解在当前的默认编译器后端与新的 [IR 编译器](js-ir-compiler.html)后端中可用。如果以 IR 编译器后端为目标，则 **必须** 使用 `@JsExport` 批注使函数首先在 Kotlin 中可见。

对于多平台项目，`@JsExport` 也可以在公共代码中使用。它仅在针对 JavaScript 目标进行编译时才有效，并且还允许导出不是特定于平台的 Kotlin 声明。

## 在 JavaScript 中表示 Kotlin 类型

* 除了 `kotlin.Long` 的 Kotlin 数字类型映射到 JavaScript Number。
* `kotlin.Char` 映射到 JavaScript Number 来表示字符代码。
* Kotlin 在运行时无法区分数字类型（`kotlin.Long` 除外），因此以下代码能够工作：
  <div class="sample" markdown="1" theme="idea" data-highlight-only>
  ```kotlin
  fun f() {
      val x: Int = 23
      val y: Any = x
      println(y as Float)
  }
  ```
  </div>

* Kotlin 保留了 `kotlin.Int`、 `kotlin.Byte`、 `kotlin.Short`、 `kotlin.Char` 和 `kotlin.Long` 的溢出语义。
* `kotlin.Long` 没有映射到任何 JavaScript 对象，因为 JavaScript 中没有 64 位整数，它是由一个 Kotlin 类模拟的。
* `kotlin.String` 映射到 JavaScript String。
* `kotlin.Any` 映射到 JavaScript Object（`new Object()`、 `{}` 等）。
* `kotlin.Array` 映射到 JavaScript Array。
* Kotlin 集合（`List`、 `Set`、 `Map` 等）没有映射到任何特定的 JavaScript 类型。
* `kotlin.Throwable` 映射到 JavaScript Error。
* Kotlin 在 JavaScript 中保留了惰性对象初始化。
* Kotlin 不会在 JavaScript 中实现顶层属性的惰性初始化。

自 1.1.50 版起，原生数组转换到 JavaScript 时采用 TypedArray：

* `kotlin.ByteArray`、 `-.ShortArray`、 `-.IntArray`、 `-.FloatArray` 以及 `-.DoubleArray` 会相应地映射为
   JavaScript 中的 Int8Array、 Int16Array、 Int32Array、 Float32Array 以及 Float64Array。
* `kotlin.BooleanArray` 会映射为 JavaScript 中具有 `$type$ == "BooleanArray"` 属性的 Int8Array
* `kotlin.CharArray` 会映射为 JavaScript 中具有 `$type$ == "CharArray"` 属性的 UInt16Array
* `kotlin.LongArray` 会映射为 JavaScript 中具有 `$type$ == "LongArray"` 属性的 `kotlin.Long` 的数组。

