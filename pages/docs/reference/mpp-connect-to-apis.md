---
type: doc
layout: reference
title: "接入平台相关 API"
---

# 接入平台相关 API

> `expect`/`actual` 特性目前处于 [Beta](evolution/components-stability.html) 中。
  本文档中描述的所有语言与工具特性都可能在将来的 Kotlin 版本中进行改变。
{:.note}

如果开发的多平台应用程序需要访问实现所需功能的特定于平台的 API，
可以使用 Kotlin *预期声明与实际声明* 机制。

通过这种机制，公共源集定义了 *预期声明*，
并且平台源集必须提供与预期声明相对应的 *实际声明*。
这适用于大多数 Kotlin 声明，例如，函数、类、接口、枚举、属性与注释。

![预期声明与实际声明]({{ url_for('asset', path='images/reference/mpp/expect-actual.png') }})
 
<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
//Common
expect fun randomUUID(): String
```

</div>

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
//Android
import java.util.*
actual fun randomUUID() = UUID.randomUUID().toString()
```

</div>

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
//iOS
import platform.Foundation.NSUUID
actual fun randomUUID(): String = NSUUID().UUIDString()
```

</div>

这是在最小化日志记录框架中通用与平台逻辑之间的代码共享与交互的另一个示例。


<div style="display:flex">
<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
//Common
enum class LogLevel {
    DEBUG, WARN, ERROR
}

internal expect fun writeLogMessage(message: String, logLevel: LogLevel)

fun logDebug(message: String) = writeLogMessage(message, LogLevel.DEBUG)
fun logWarn(message: String) = writeLogMessage(message, LogLevel.WARN)
fun logError(message: String) = writeLogMessage(message, LogLevel.ERROR)
```

</div>
<div style="margin-left: 5px;white-space: pre-line; line-height: 18px; font-family: Tahoma;">
    <div style="display:flex">├<i style="margin-left:5px">针对所有平台编译</i></div>
    <div style="display:flex">├<i style="margin-left:5px">预期平台特定的 API</i></div>
    <div style="display:flex">├<i style="margin-left:5px">可以在公共代码中使用预期的 API</i></div>
</div>
</div>

它预期目标为 `writeLogMessage` 提供特定于平台的实现，
并且公共代码现在可以使用此声明，而无需考虑如何实现。

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
//JVM
internal actual fun writeLogMessage(message: String, logLevel: LogLevel) {
    println("[$logLevel]: $message")
}
```

</div>

对于 JavaScript，可以使用一组完全不同的 API，`actual` 声明将如下所示。

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
//JS
internal actual fun writeLogMessage(message: String, logLevel: LogLevel) {
    when (logLevel) {
        LogLevel.DEBUG -> console.log(message)
        LogLevel.WARN -> console.warn(message)
        LogLevel.ERROR -> console.error(message)
    }
}
```

</div>

关于预期声明与实际声明的主要规则为：
* 预期声明用关键字 `expect` 标记；实际声明用 `actual` 关键字标记。
* `expect` 与 `actual` 声明具有相同的名称，并且位于同一包中（具有相同的完全限定名称）。
* `expect` 声明绝不包含任何实现代码。

在每个平台编译期间，
编译器确保在公共或中间源集中的每个标有 `expect` 关键字的声明在所有平台源集中都有对应的标有 `actual` 关键字的声明。
IDE 提供的工具可以帮助创建缺失的实际声明。

如果要在共享代码中使用特定于平台的库，同时为另一个平台提供自己的实现，
那么可以为现有类提供 `typealias` 作为实际声明：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
expect class AtomicRef<V>(value: V) {
    fun get(): V
    fun set(value: V)
    fun getAndSet(value: V): V
    fun compareAndSet(expect: V, update: V): Boolean
}
```

</div>

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
actual typealias AtomicRef<V> = java.util.concurrent.atomic.AtomicReference<V>
```

</div>

> 建议仅对具有特定于平台依赖项的 Kotlin 声明使用预期声明与实际声明。
> 最好在共享模块中实现尽可能多的功能，
> 即使这样做会花费更多时间。
> 
> 不要过度使用预期声明与实际声明
> ——在某些情况下，[接口](interfaces.html)可能会是更好的选择，因为它更加灵活且易于测试。
{:.note}
