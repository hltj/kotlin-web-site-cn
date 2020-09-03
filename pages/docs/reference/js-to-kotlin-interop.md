---
type: doc
layout: reference
category: "JavaScript"
title: "JavaScript 中调用 Kotlin"
---

# JavaScript 中调用 Kotlin

Depending on the selected [JavaScript Module](js-modules.html) system, the Kotlin/JS compiler generates different output. 当然通常 Kotlin 编译器生成正常的 JavaScript 类，可以在 JavaScript 代码中自由地使用的函数和属性。不过，你应该记住一些微妙的事情。

## 在 `plain` 模式中用独立的 JavaScript 隔离声明
If you have explicitly set your module kind to be `plain`, 为了防止损坏全局对象，Kotlin 创建一个包含当前模块中所有 Kotlin 声明的对象。这意味着对于一个模块 `myModule`，所有的声明都可以通过 `myModule` 对象在 JavaScript 中使用。例如：

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

This is not applicable when you compile your Kotlin module to JavaScript modules like UMD (which is the default setting for both `browser` and `nodejs` targets), CommonJS or AMD. In this case, your declarations will be exposed in the format specified by your chosen JavaScript module system. When using UMD or CommonJS, for example, your call site could look like this:

<div class="sample" markdown="1" theme="idea" data-highlight-only>
``` javascript
alert(require('myModule').foo());
```
</div>

Check the article on [JavaScript Modules](js-modules.html) for more information on the topic of JavaScript module systems.

## 包结构

Kotlin 将其包结构暴露给 JavaScript，因此除非你在根包中定义声明，
否则必须在 JavaScript 中使用完整限定名。例如：

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
package my.qualified.packagename

fun foo() = "Hello"
```
</div>

When using UMD or CommonJS, for example, your callsite could look like this:

<div class="sample" markdown="1" theme="idea" data-highlight-only>
``` javascript
alert(require('myModule').my.qualified.packagename.foo())
```
</div>

Or, in the case of using `plain` as a module system setting:

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
// If necessary, import 'kjs' according to chosen module system
var person = new kjs.Person("Dmitry");   // 引用到模块“kjs”
person.hello();                          // 输出“Hello Dmitry!”
person.helloWithGreeting("Servus");      // 输出“Servus Dmitry!”
```
</div>

如果我们没有指定 `@JsName` 注解，相应函数的名称会包含<!--
-->从函数签名计算而来的后缀，例如 `hello_61zpoe$`。

Note that there are some cases in which the Kotlin compiler does not apply mangling:
- `external` declarations are not mangled.
- Any overridden functions in non-`external` classes inheriting from `external` classes are not mangled.


`@JsName` 的参数需要是一个常量字符串字面值，该字面值是一个有效的标识符。
任何尝试将非标识符字符串传递给 `@JsName` 时，编译器都会报错。
以下示例会产生编译期错误：

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
@JsName("new C()")   // 此处出错
external fun newC()
```
</div>

### `@JsExport` annotation
> The `@JsExport` annotation is currently marked as experimental. Its design may change in future versions.
{:.note} 

By applying the `@JsExport` annotation to a top-level declaration (like a class or function), you make the Kotlin declaration available from JavaScript. The annotation exports all nested declarations with the name given in Kotlin. It can also be applied on file-level using `@file:JsExport`.

To resolve ambiguities in exports (like overloads for functions with the same name), you can use the `@JsExport` annotation together with `@JsName` to specify the names for the generated and exported functions.

The `@JsExport` annotation is available in the current default compiler backend and the new [IR compiler backend](js-ir-compiler.html). If you are targeting the IR compiler backend, you **must** use the `@JsExport` annotation to make your functions visible from Kotlin in the first place.

For multiplatform projects, `@JsExport` is available in common code as well. It only has an effect when compiling for the JavaScript target, and allows you to also export Kotlin declarations that are not platform specific.

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

