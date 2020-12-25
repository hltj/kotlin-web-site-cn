---
type: doc
layout: reference
category: "JavaScript"
title: "Kotlin 中调用 JavaScript"
---

# Kotlin 中调用 JavaScript

Kotlin 最初被设计为能够与 Java 平台轻松互操作。它将 Java 类视为 Kotlin 类，并且 Java 也将 Kotlin 类视为 Java 类。

但是，JavaScript 是一种动态类型语言，这意味着它不会在编译期检测类型。可以通过[动态](dynamic-type.html)类型在 Kotlin 中自由地与 JavaScript 交流。如果想要使用 Kotlin 类型系统的全部威力，可以为 JavaScript 库创建 Kotlin 编译器与周边工具可理解的外部声明。

还提供了一种实验工具，可为 npm 依赖自动创建 Kotlin 外部声明，该声明提供称为 [Dukat](js-external-declarations-with-dukat) 的类型定义（TypeScript / `d.ts`）。

## 内联 JavaScript

你可以使用 [`js("……")`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.js/js.html) 函数将一些 JavaScript 代码嵌入到 Kotlin 代码中。
例如：

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
fun jsTypeOf(o: Any): String {
    return js("typeof o")
}
```
</div>

因为 `js` 的参数是在编译期解析并且按原样翻译成 JavaScript 代码的，因此它必须是字符串常量。因此，以下代码是不正确的：

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
fun jsTypeOf(o: Any): String {
    return js(getTypeof() + " o") // 此处报错
}
fun getTypeof() = "typeof"
```
</div>

请注意，调用 `js()` 会返回动态类型的结果，该结果在编译时不提供任何类型安全性。

## `external` 修饰符

要告诉 Kotlin 某个声明是用纯 JavaScript 编写的，你应该用 `external` 修饰符来标记它。
当编译器看到这样的声明时，它假定相应类、函数或属性的实现是由外部提供的（由开发人员或者通过 [npm 依赖项](js-project-setup.html#npm-dependencies)），因此不会尝试从声明中生成任何 JavaScript 代码。
这也是为什么 `external` 声明不能具有主体的原因。例如：

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
external fun alert(message: Any?): Unit

external class Node {
    val firstChild: Node

    fun append(child: Node): Node

    fun removeChild(child: Node): Node

    // 等等
}

external val window: Window
```
</div>

请注意，嵌套的声明会继承 `external` 修饰符，这也是 `Node` 类中成员函数和属性之前不需要加 `external` 修饰符的原因。

`external` 修饰符只允许在包级声明中使用。 你不能声明一个非 `external` 类的 `external` 成员。


### 声明类的（静态）成员

在 JavaScript 中，你可以在原型或者类本身上定义成员：

<div class="sample" markdown="1" theme="idea" data-highlight-only>
``` javascript
function MyClass() { …… }
MyClass.sharedMember = function() { /* 实现 */ };
MyClass.prototype.ownMember = function() { /* 实现 */ };
```
</div>

Kotlin 中没有这样的语法。然而，在 Kotlin 中我们有[伴生（`companion`）对象](object-declarations.html#companion-objects)。Kotlin 以特殊的方式处理
`external` 类的伴生对象：替代期待一个对象的是，它假定伴生对象的成员就是该类自身的成员。可以这样描述来自上例中的 `MyClass`：

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
external class MyClass {
    companion object {
        fun sharedMember()
    }

    fun ownMember()
}
```
</div>


### 声明可选参数

如果正在为具有可选参数的 JavaScript 函数编写外部声明，请使用 `definedExternally`。这将默认值的生成委托给 JavaScript 函数本身：

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
external fun myFunWithOptionalArgs(
    x: Int,
    y: String = definedExternally,
    z: String = definedExternally
)
```
</div>

使用此外部声明，可以调用带有一个必需参数和两个可选参数的 `myFunWithOptionalArgs`，其中默认值由 `myFunWithOptionalArgs` 的 JavaScript 实现计算得出。


### 扩展 JavaScript 类

你可以轻松扩展 JavaScript 类，因为它们是 Kotlin 类。只需定义一个 `external open` 类并用<!--
-->非 `external` 类扩展它。例如：

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
open external class Foo {
    open fun run()
    fun stop()
}

class Bar: Foo() {
    override fun run() {
        window.alert("Running!")
    }

    fun restart() {
        window.alert("Restarting")
    }
}
```
</div>

有一些限制：

- 当一个外部基类的函数被签名重载时，不能在派生类中覆盖它。
- 不能覆盖一个使用默认参数的函数。
- 不能用外部类扩展非外部类。

### `external` 接口

JavaScript 没有接口的概念。当函数期望其参数支持 `foo`
和 `bar` 两个方法时，只需传入实际具有这些方法的对象。

在静态类型的 Kotlin 中，你可以使用接口来表达这一概念：

<div class="sample" markdown="1" theme="idea" data-highlight-only>
```kotlin
external interface HasFooAndBar {
    fun foo()

    fun bar()
}

external fun myFunction(p: HasFooAndBar)
```
</div>

外部接口的典型使用场景是描述设置对象。例如：

<div class="sample" markdown="1" theme="idea" data-highlight-only auto-indent="false">
```kotlin
external interface JQueryAjaxSettings {
    var async: Boolean

    var cache: Boolean

    var complete: (JQueryXHR, String) -> Unit

    // 等等
}

fun JQueryAjaxSettings(): JQueryAjaxSettings = js("{}")

external class JQuery {
    companion object {
        fun get(settings: JQueryAjaxSettings): JQueryXHR
    }
}

fun sendQuery() {
    JQuery.get(JQueryAjaxSettings().apply {
        complete = { (xhr, data) ->
            window.alert("Request complete")
        }
    })
}
```
</div>

外部接口有一些限制：

- 它们不能在 `is` 检测的右侧使用。
- 它们不能作为具体化类型参数传递。
- 它们不能用在类的字面值表达式（例如 `I::class`）中。
- `as` 转换为外部接口总是成功。
    强制转换为外部接口会产生“未检查强制转换到外部接口（Unchecked cast to external interface）”编译时警告。可以使用 `@Suppress("UNCHECKED_CAST_TO_EXTERNAL_INTERFACE")` 注解取消警告。

    IntelliJ IDEA 还可以自动生成 `@Suppress` 注解。通过灯泡图标或 <kbd>Alt</kbd> + <kbd>Enter</kbd> 打开意图菜单，然后单击“未检查强制转换到外部接口”检查旁边的小箭头。在这里，可以选择抑制作用域，IDE 将相应地将注解添加到文件中。

### 强制转换
除了[“unsafe”强制转换运算符](/docs/reference/typecasts.html#unsafe-cast-operator) `as`（在无法进行强制转换时抛出 `ClassCastException`）之外，Kotlin/JS 还提供 [`unsafeCast<T>()`](/api/latest/jvm/stdlib/kotlin.js/unsafe-cast.html)。使用 `unsafeCast` 时，在运行时 _完全不进行类型检查_。例如，考虑以下两种方法：

<div class="sample" markdown="1" theme="idea" data-highlight-only auto-indent="false">

```kotlin
fun usingUnsafeCast(s: Any) = s.unsafeCast<String>()
fun usingAsOperator(s: Any) = s as String
```
</div>

它们将进行相应的编译：
<div class="sample" markdown="1" theme="idea" mode="java">

``` javascript
function usingUnsafeCast(s) {
    return s;
}

function usingAsOperator(s) {
    var tmp$;
    return typeof (tmp$ = s) === 'string' ? tmp$ : throwCCE();
}
```

</div>
