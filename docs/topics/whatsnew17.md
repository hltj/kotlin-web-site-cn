[//]: # (title: Kotlin 1.7.0 的新特性)

_[发布日期：2022-07-09](releases.md#版本发布详情)_

Kotlin 1.7.0 has been released. It unveils the Alpha version of the new Kotlin/JVM K2 compiler, stabilizes language
features, and brings performance improvements for the JVM, JS, and Native platforms.

Here is a list of the major updates in this version:

* [The new Kotlin K2 compiler is in Alpha now](#用于-jvm-的新版-kotlin-k2-编译器进入-alpha-阶段), and it offers serious
  performance improvements. It is available only for the JVM, and none of the compiler plugins, including kapt, work with it.
* [A new approach to the incremental compilation in Gradle](#增量编译的新方式). Incremental
  compilation is now also supported for changes made inside dependent non-Kotlin modules and is compatible with Gradle.
* We've stabilized [opt-in requirement annotations](#稳定版选择加入要求), [definitely non-nullable types](#稳定版绝对不可空类型),
  and [builder inference](#稳定版构建器推断).
* [There's now an underscore operator for type args](#下划线操作符用于类型参数). You can use it to
  automatically infer a type of argument when other types are specified.
* [This release allows implementation by delegation to an inlined value of an inline class](#内联类的内联值可以通过委托来实现). You can now create
  lightweight wrappers that do not allocate memory in most cases.

You can also find a short overview of the changes in this video:

<video href="54WEfLKtCGk" title="What's new in Kotlin 1.7.0"/>

## 用于 JVM 的新版 Kotlin K2 编译器进入 Alpha 阶段

This Kotlin release introduces the **Alpha** version of the new Kotlin K2 compiler. The new compiler aims to speed up
the development of new language features, unify all of the platforms Kotlin supports, bring performance improvements, and
provide an API for compiler extensions.

We've already published some detailed explanations of our new compiler and its benefits:

* [The Road to the New Kotlin Compiler](https://www.youtube.com/watch?v=iTdJJq_LyoY)
* [K2 Compiler: a Top-Down View](https://www.youtube.com/watch?v=db19VFLZqJM)

It's important to point out that with the Alpha version of the new K2 compiler we were primarily focused on performance
improvements, and it only works with JVM projects. It doesn't support Kotlin/JS, Kotlin/Native, or other multi-platform
projects, and none of compiler plugins, including [kapt](kapt.md), work with it.

Our benchmarks show some outstanding results on our internal projects:

| Project       | Current Kotlin compiler performance | New K2 Kotlin compiler performance | Performance boost |
|---------------|-------------------------------------|------------------------------------|-------------------|
| Kotlin        | 2.2 KLOC/s                          | 4.8 KLOC/s                         | ~ x2.2            |
| YouTrack      | 1.8 KLOC/s                          | 4.2 KLOC/s                         | ~ x2.3            |
| IntelliJ IDEA | 1.8 KLOC/s                          | 3.9 KLOC/s                         | ~ x2.2            |
| Space         | 1.2 KLOC/s                          | 2.8 KLOC/s                         | ~ x2.3            |

> The KLOC/s performance numbers stand for the number of thousands of lines of code that the compiler processes per
> second.
>
> {type="tip"}

You can check out the performance boost on your JVM projects and compare it with the results of the old compiler. To
enable the Kotlin K2 compiler, use the following compiler option:

```bash
-Xuse-k2
```

Also, the K2 compiler [includes a number of bugfixes](https://youtrack.jetbrains.com/issues/KT?q=tag:%20fixed-in-frontend-ir%20sort%20by:%20Priority,%20votes,%20updated).
Please note that even issues with **State: Open** from this list are in fact fixed in K2.

The next Kotlin releases will improve the stability of the K2 compiler and provide more features, so stay tuned!

If you face any performance issues with the Kotlin K2 compiler, please [report them to our issue tracker](https://youtrack.jetbrains.com/newIssue?project=KT&c=Type%20Performance%20Problem&c=Subsystems%20Frontend.%20IR).

## 语言

Kotlin 1.7.0 introduces support for implementation by delegation and a new underscore operator for type arguments. It
also stabilizes several language features introduced as previews in previous releases:

* [用委托实现内联类的内联值](#内联类的内联值可以通过委托来实现)
* [下划线操作符用于类型参数](#下划线操作符用于类型参数)
* [稳定版构建器推断](#稳定版构建器推断)
* [稳定版选择加入要求](#稳定版选择加入要求)
* [稳定版绝对不可空类型](#稳定版绝对不可空类型)

### 内联类的内联值可以通过委托来实现

If you want to create a lightweight wrapper for a value or class instance, it's necessary to implement all interface
methods by hand. Implementation by delegation solves this issue, but it did not work with inline classes before 1.7.0.
This restriction has been removed, so you can now create lightweight wrappers that do not allocate memory in most cases.

```kotlin
interface Bar {
    fun foo() = "foo"
}

@JvmInline
value class BarWrapper(val bar: Bar): Bar by bar

fun main() {
    val bw = BarWrapper(object: Bar {})
    println(bw.foo())
}
```

### 下划线操作符用于类型参数

Kotlin 1.7.0 introduces an underscore operator, `_`, for type arguments. You can use it to automatically infer a type
argument when other types are specified:

```kotlin
abstract class SomeClass<T> {
    abstract fun execute(): T
}

class SomeImplementation : SomeClass<String>() {
    override fun execute(): String = "Test"
}

class OtherImplementation : SomeClass<Int>() {
    override fun execute(): Int = 42
}

object Runner {
    inline fun <reified S: SomeClass<T>, T> run(): T {
        return S::class.java.getDeclaredConstructor().newInstance().execute()
    }
}

fun main() {
    // T is inferred as String because SomeImplementation derives from SomeClass<String>
    val s = Runner.run<SomeImplementation, _>()
    assert(s == "Test")

    // T is inferred as Int because OtherImplementation derives from SomeClass<Int>
    val n = Runner.run<OtherImplementation, _>()
    assert(n == 42)
}
```

> You can use the underscore operator in any position in the variables list to infer a type argument.
>
{type="note"}

### 稳定版构建器推断

Builder inference is a special kind of type inference that is useful when calling generic builder functions. It helps
the compiler infer the type arguments of a call using the type information about other calls inside its lambda argument.

Starting with 1.7.0, builder inference is automatically activated if a regular type inference cannot get enough
information about a type without specifying the `-Xenable-builder-inference` compiler option, which
was [introduced in 1.6.0](whatsnew16.md#构建器类型推断变更).

[Learn how to write custom generic builders](using-builders-with-builder-inference.md).

### 稳定版选择加入要求

[Opt-in requirements](opt-in-requirements.md) are now [Stable](components-stability.md) and do not require
additional compiler configuration.

Before 1.7.0, the opt-in feature itself required the argument `-opt-in=kotlin.RequiresOptIn` to avoid a warning. It no
longer requires this; however, you can still use the compiler argument `-opt-in` to opt-in for other
annotations, [module-wise](opt-in-requirements.md#模块级选择加入).

### 稳定版绝对不可空类型

In Kotlin 1.7.0, definitely non-nullable types have been promoted to [Stable](components-stability.md). They provide
better interoperability when extending generic Java classes and interfaces.

You can mark a generic type parameter as definitely non-nullable at the use site with the new syntax `T & Any.` The
syntactic form comes from the notation for [intersection types](https://en.wikipedia.org/wiki/Intersection_type) and is
now limited to a type parameter with nullable upper bounds on the left side of `&` and a non-nullable `Any` on the right
side:

```kotlin
fun <T> elvisLike(x: T, y: T & Any): T & Any = x ?: y

fun main() {
    // OK
    elvisLike<String>("", "").length
    // Error: 'null' cannot be a value of a non-null type
    elvisLike<String>("", null).length

    // OK
    elvisLike<String?>(null, "").length
    // Error: 'null' cannot be a value of a non-null type
    elvisLike<String?>(null, null).length
}
```

Learn more about definitely non-nullable types
in [this KEEP](https://github.com/Kotlin/KEEP/blob/c72601cf35c1e95a541bb4b230edb474a6d1d1a8/proposals/definitely-non-nullable-types.md).

## Kotlin/JVM

This release brings performance improvements for the Kotlin/JVM compiler and a new compiler option. Additionally,
callable references to functional interface constructors have become Stable. Note that since 1.7.0, the default target
version for Kotlin/JVM compilations is now `1.8`.

* [编译器性能优化](#编译器性能优化)
* [新增编译器选项 `-Xjdk-release`](#新增编译器选项-xjdk-release)
* [稳定版函数式接口构造函数的可调用引用](#稳定版函数式接口构造函数的可调用引用)
* [删除了 JVM 目标平台版本 1.6](#删除了-jvm-目标平台版本-1-6)

### 编译器性能优化

Kotlin 1.7.0 introduces performance improvements for the Kotlin/JVM compiler. According to our benchmarks, compilation
time has been [reduced by 10% on average](https://youtrack.jetbrains.com/issue/KT-48233/Switching-to-JVM-IR-backend-increases-compilation-time-by-more-t#focus=Comments-27-6114542.0-0)
compared to Kotlin 1.6.0. Projects with lots of usages of inline functions, for
example, [projects using `kotlinx.html`](https://youtrack.jetbrains.com/issue/KT-51416/Compilation-of-kotlinx-html-DSL-should-still-be-faster),
will compile faster thanks to the improvements to the bytecode postprocessing.

### 新增编译器选项：-Xjdk-release

Kotlin 1.7.0 presents a new compiler option, `-Xjdk-release`. This option is similar to
the [javac's command-line `--release` option](http://openjdk.java.net/jeps/247). The `-Xjdk-release` option controls the
target bytecode version and limits the API of the JDK in the classpath to the specified Java version. For
example, `kotlinc -Xjdk-release=1.8` won't allow referencing `java.lang.Module` even if the JDK in the dependencies is
version 9 or higher.

> This option is [not guaranteed](https://youtrack.jetbrains.com/issue/KT-29974) to be effective for each JDK distribution.
>
{type="note"}

Please leave your feedback
on [this YouTrack ticket](https://youtrack.jetbrains.com/issue/KT-29974/Add-a-compiler-option-Xjdk-release-similar-to-javac-s-release-to).

### 稳定版函数式接口构造函数的可调用引用

[Callable references](reflection.md#可调用引用) to functional interface constructors are
now [Stable](components-stability.md). Learn how
to [migrate](fun-interfaces.md#migration-from-an-interface-with-constructor-function-to-a-functional-interface)
from an interface with a constructor function to a functional interface using callable references.

Please report any issues you find in [YouTrack](https://youtrack.jetbrains.com/newissue?project=kt).

### 删除了 JVM 目标平台版本 1.6

The default target version for Kotlin/JVM compilations is `1.8`. The `1.6` target has been removed.

Please migrate to JVM target 1.8 or above. Learn how to update the JVM target version for:

* [Gradle](gradle.md#jvm-特有的属性)
* [Maven](maven.md#jvm-特有的属性)
* [The command-line compiler](compiler-reference.md#jvm-target-version)

## Kotlin/Native

Kotlin 1.7.0 includes changes to Objective-C and Swift interoperability and stabilizes features that were introduced in
previous releases. It also brings performance improvements for the new memory manager along with other updates:

* [新版内存管理器的性能改进](#新版内存管理器的性能改进)
* [与 JVM 及 JS IR 后端统一编译器插件 ABI](#与-jvm-及-js-ir-后端统一编译器插件-abi)
* [支持独立的 Android 可执行文件](#支持独立的-android-可执行文件)
* [与 Swift async/await 互操作：返回 `Void` 而不是 `KotlinUnit`](#与-swift-async-await-互操作返回-void-而不是-kotlinunit)
* [通过 Objective-C 桥接禁止未声明的异常](#通过-objective-c-桥接禁止未声明的异常)
* [改进了 CocoaPods 集成](#改进了-cocoapods-集成)
* [覆盖了 Kotlin/Native 编译器下载 URL](#覆盖了-kotlin-native-编译器下载-url)

### 新版内存管理器的性能改进

> The new Kotlin/Native memory manager is in [Alpha](components-stability.md).
> It may change incompatibly and require manual migration in the future.
> We would appreciate your feedback in [YouTrack](https://youtrack.jetbrains.com/issue/KT-48525).
>
{type="note"}

The new memory manager is still in Alpha, but it is on its way to becoming [Stable](components-stability.md).
This release delivers significant performance improvements for the new memory manager, especially in garbage
collection (GC). In particular, concurrent implementation of the sweep phase, [introduced in 1.6.20](whatsnew1620.md),
is now enabled by default. This helps reduce the time the application is paused for GC. The new GC scheduler is better
at choosing the GC frequency, especially for larger heaps.

Also, we've specifically optimized debug binaries, ensuring that the proper optimization level and link-time
optimizations are used in the implementation code of the memory manager. This helped us improve execution time by
roughly 30% for debug binaries on our benchmarks.

Try using the new memory manager in your projects to see how it works, and share your feedback with us
in [YouTrack](https://youtrack.jetbrains.com/issue/KT-48525).

### 与 JVM 及 JS IR 后端统一编译器插件 ABI

Starting with Kotlin 1.7.0, the Kotlin Multiplatform Gradle plugin uses the embeddable compiler jar for Kotlin/Native by
default. This [feature was announced in 1.6.0](whatsnew16.md#与-jvm-及-js-ir-后端统一编译器插件-abi) as
Experimental, and now it's stable and ready to use.

This improvement is very handy for library authors, as it improves the compiler plugin development experience. Before
this release, you had to provide separate artifacts for Kotlin/Native, but now you can use the same compiler plugin
artifacts for Native and other supported platforms.

> This feature might require plugin developers to take migration steps for their existing plugins.
>
> Learn how to prepare your plugin for the update in
> this [YouTrack issue](https://youtrack.jetbrains.com/issue/KT-48595).
>
{type="warning"}

### 支持独立的 Android 可执行文件

Kotlin 1.7.0 provides full support for generating standard executables for Android Native targets.
It was [introduced in 1.6.20](whatsnew1620.md#支持独立的-android-可执行文件), and now it's enabled
by default.

If you want to roll back to the previous behavior when Kotlin/Native generated shared libraries, use the following
setting:

```kotlin
binaryOptions["androidProgramType"] = "nativeActivity"
```

### 与 Swift async/await 互操作：返回 Void 而不是 KotlinUnit

Kotlin `suspend` functions now return the `Void` type instead of `KotlinUnit` in Swift. This is the result of the
improved interop with Swift's `async`/`await`. This feature
was [introduced in 1.6.20](whatsnew1620.md#与-swift-async-await-互操作返回-void-而不是-kotlinunit),
and this release enables this behavior by default.

You don't need to use the `kotlin.native.binary.unitSuspendFunctionObjCExport=proper` property anymore to return the
proper type for such functions.

### 通过 Objective-C 桥接禁止未声明的异常

When you call Kotlin code from Swift/Objective-C code (or vice versa) and this code throws an exception, it should be
handled by the code where the exception occurred, unless you specifically allowed the forwarding of exceptions between
languages with proper conversion (for example, using the `@Throws` annotation).

Previously, Kotlin had another unintended behavior where undeclared exceptions could "leak" from one language to another
in some cases. Kotlin 1.7.0 fixes that issue, and now such cases lead to program termination.

So, for example, if you have a `{ throw Exception() }` lambda in Kotlin and call it from Swift, in Kotlin 1.7.0 it will
terminate as soon as the exception reaches the Swift code. In previous Kotlin versions, such an exception could leak to
the Swift code.

The `@Throws` annotation continues to work as before.

### 改进了 CocoaPods 集成

Starting with Kotlin 1.7.0, you no longer need to install the `cocoapods-generate` plugin if you want to integrate
CocoaPods in your projects.

Previously, you needed to install both the CocoaPods dependency manager and the `cocoapods-generate` plugin to use
CocoaPods, for example, to handle [iOS dependencies](multiplatform-mobile-ios-dependencies.md#with-cocoapods) in
Kotlin Multiplatform Mobile projects.

Now setting up the CocoaPods integration is easier, and we've resolved the issue when `cocoapods-generate` couldn't be
installed on Ruby 3 and later. Now the newest Ruby versions that work better on Apple M1 are also supported.

See how to set up
the [initial CocoaPods integration](native-cocoapods.md#set-up-an-environment-to-work-with-cocoapods).

### 覆盖了 Kotlin/Native 编译器下载 URL

Starting with Kotlin 1.7.0, you can customize the download URL for the Kotlin/Native compiler. This is useful when
external links on the CI are forbidden.

To override the default base URL `https://download.jetbrains.com/kotlin/native/builds`, use the following Gradle
property:

```properties
kotlin.native.distribution.baseDownloadUrl=https://example.com
```

> The downloader will append the native version and target OS to this base URL to ensure it downloads the actual
> compiler distribution.
>
{type="note"}

## Kotlin/JS

Kotlin/JS is receiving further improvements to the [JS IR compiler backend](js-ir-compiler.md) along with other updates
that can make your development experience better:

* [新版 IR 后端的性能改进](#新版-ir-后端的性能改进)
* [使用 IR 时的成员名缩短](#使用-ir-时的成员名缩短)
* [通过 IR 后端的 polyfill 支持旧版浏览器](#通过-ir-后端的-polyfill-支持旧版浏览器)
* [从 js 表达式动态加载 JavaScript 模块](#从-js-表达式动态加载-javascript-模块)
* [为 JavaScript 测试运行器指定环境变量](#为-javascript-测试运行器指定环境变量)

### 新版 IR 后端的性能改进

This release has some major updates that should improve your development experience:

* Incremental compilation performance of Kotlin/JS has been significantly improved. It takes less time to build your JS
  projects. Incremental rebuilds should now be roughly on par with the legacy backend in many cases now.
* The Kotlin/JS final bundle requires less space, as we have significantly reduced the size of the final artifacts.
  We've measured up to a 20% reduction in the production bundle size compared to the legacy backend for some large
  projects.
* Type checking for interfaces has been improved by orders of magnitude.
* Kotlin generates higher-quality JS code

### 使用 IR 时的成员名缩短

The Kotlin/JS IR compiler now uses its internal information about the relationships of your Kotlin classes and functions
to apply more efficient minification, shortening the names of functions, properties, and classes. This shrinks the
resulting bundled applications.

This type of minification is automatically applied when you build your Kotlin/JS application in production mode and is
enabled by default. To disable member name minification, use the `-Xir-minimized-member-names` compiler flag:

```kotlin
kotlin {
    js(IR) {
        compilations.all {
            compileKotlinTask.kotlinOptions.freeCompilerArgs += listOf("-Xir-minimized-member-names=false")
        }
    }
}
```

### 通过 IR 后端的 polyfill 支持旧版浏览器

The IR compiler backend for Kotlin/JS now includes the same polyfills as the legacy backend. This allows code compiled
with the new compiler to run in older browsers that do not support all the methods from ES2015 used by the Kotlin
standard library. Only those polyfills actually used by the project are included in the final bundle, which minimizes
their potential impact on the bundle size.

This feature is enabled by default when using the IR compiler, and you don't need to configure it.

### 从 js 表达式动态加载 JavaScript 模块

When working with the JavaScript modules, most applications use static imports, whose use is covered with
the [JavaScript module integration](js-modules.md). However, Kotlin/JS was missing a mechanism to load JavaScript
modules dynamically at runtime in your applications.

Starting with Kotlin 1.7.0, the `import` statement from JavaScript is supported in `js` blocks, allowing you to
dynamically bring packages into your application at runtime:

```kotlin
val myPackage = js("import('my-package')")
```

### 为 JavaScript 测试运行器指定环境变量

To tune Node.js package resolution or pass external information to Node.js tests, you can now specify environment
variables used by the JavaScript test runners. To define an environment variable, use the `environment()` function with
a key-value pair inside the `testTask` block in your build script:

```kotlin
kotlin {
    js {
        nodejs {
            testTask {
                environment("key", "value")
            }
        }
    }
}
```

## 标准库

In Kotlin 1.7.0, the standard library has received a range of changes and improvements. They introduce new features,
stabilize experimental ones, and unify support for named capturing groups for Native, JS, and the JVM:

* [集合函数 min() 与 max() 返回不可空值](#集合函数-min-与-max-返回不可空值)
* [指定索引位置的正则表达式匹配](#指定索引位置的正则表达式匹配)
* [对以前语言与 API 版本的扩展支持](#对以前语言与-api-版本的扩展支持)
* [通过反射访问注解](#通过反射访问注解)
* [稳定版深递归函数](#稳定版深递归函数)
* [默认时间源的时间标记基于内联类](#默认时间源的时间标记基于内联类)
* [Java Optional 的新实验性扩展函数](#java-optional-的新实验性扩展函数)
* [在 JS 与 Native 平台支持命名捕获](#在-js-与-native-平台支持命名捕获)

### 集合函数 min() 与 max() 返回不可空值

In [Kotlin 1.4.0](whatsnew14.md), we renamed the `min()` and `max()` collection functions to `minOrNull()`
and `maxOrNull()`. These new names better reflect their behavior – returning null if the receiver collection is empty.
It also helped align the functions' behavior with naming conventions used throughout the Kotlin collections API.

The same was true of `minBy()`, `maxBy()`, `minWith()`, and `maxWith()`, which all got their *OrNull() synonyms in
Kotlin 1.4.0. Older functions affected by this change were gradually deprecated.

Kotlin 1.7.0 reintroduces the original function names, but with a non-nullable return type. The new `min()`, `max()`
, `minBy()`, `maxBy()`, `minWith()`, and `maxWith()` functions now strictly return the collection element or throw an
exception.

```kotlin
fun main() {
    val numbers = listOf<Int>()
    println(numbers.maxOrNull()) // "null"
    println(numbers.max()) // "Exception in... Collection is empty."
}
```

### 指定索引位置的正则表达式匹配

The `Regex.matchAt()` and `Regex.matchesAt()`
functions, [introduced in 1.5.30](whatsnew1530.md#在特定位置匹配-regex), are now Stable. They
provide a way to check whether a regular expression has an exact match at a particular position in a `String`
or `CharSequence`.

`matchesAt()` checks for a match and returns a boolean result:

```kotlin
fun main() {
    val releaseText = "Kotlin 1.7.0 is on its way!"
    // regular expression: one digit, dot, one digit, dot, one or more digits
    val versionRegex = "\\d[.]\\d[.]\\d+".toRegex()

    println(versionRegex.matchesAt(releaseText, 0)) // "false"
    println(versionRegex.matchesAt(releaseText, 7)) // "true"
}
```

`matchAt()` returns the match if it's found, or `null` if it isn't:

```kotlin
fun main() {
    val releaseText = "Kotlin 1.7.0 is on its way!"
    val versionRegex = "\\d[.]\\d[.]\\d+".toRegex()

    println(versionRegex.matchAt(releaseText, 0)) // "null"
    println(versionRegex.matchAt(releaseText, 7)?.value) // "1.7.0"
}
```

We'd be grateful for your feedback on this [YouTrack issue](https://youtrack.jetbrains.com/issue/KT-34021).

### 对以前语言与 API 版本的扩展支持

To support library authors developing libraries that are meant to be consumable in a wide range of previous Kotlin
versions, and to address the increased frequency of major Kotlin releases, we have extended our support for previous
language and API versions.

With Kotlin 1.7.0, we're supporting three previous language and API versions rather than two. This means Kotlin 1.7.0
supports the development of libraries targeting Kotlin versions down to 1.4.0. For more information on backward
compatibility, see [Compatibility modes](compatibility-modes.md).

### 通过反射访问注解

The `KAnnotatedElement.[findAnnotations()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect.full/find-annotations.html)`
extension function, which was first [introduced in 1.6.0](whatsnew16.md#1-8-jvm-目标平台中运行时保留的可重复注解),
is now [Stable](components-stability.md). This [reflection](reflection.md)
function returns all annotations of a given type on an element, including individually applied and repeated annotations.

```kotlin
@Repeatable
annotation class Tag(val name: String)

@Tag("First Tag")
@Tag("Second Tag")
fun taggedFunction() {
    println("I'm a tagged function!")
}

fun main() {
    val x = ::taggedFunction
    val foo = x as KAnnotatedElement
    println(foo.findAnnotations<Tag>())
    // [@Tag(name=First Tag), @Tag(name=Second Tag)]
}
```

### 稳定版深递归函数

Deep recursive functions have been available as an experimental feature
since [Kotlin 1.4.0](https://blog.jetbrains.com/kotlin/2020/07/kotlin-1-4-rc-debugging-coroutines/#Defining_deep_recursive_functions_using_coroutines),
and they are now [Stable](components-stability.md) in Kotlin 1.7.0. Using `DeepRecursiveFunction`, you can define a
function that keeps its stack on the heap instead of using the actual call stack. This allows you to run very deep
recursive computations. To call a deep recursive function, `invoke` it.

In this example, a deep recursive function is used to calculate the depth of a binary tree recursively. Even though this
sample function calls itself recursively 100,000 times, no `StackOverflowError` is thrown:

```kotlin
class Tree(val left: Tree?, val right: Tree?)

val calculateDepth = DeepRecursiveFunction<Tree?, Int> { t ->
    if (t == null) 0 else maxOf(
        callRecursive(t.left),
        callRecursive(t.right)
    ) + 1
}

fun main() {
    // Generate a tree with a depth of 100_000
    val deepTree = generateSequence(Tree(null, null)) { prev ->
        Tree(prev, null)
    }.take(100_000).last()

    println(calculateDepth(deepTree)) // 100000
}
```

Consider using deep recursive functions in your code where your recursion depth exceeds 1000 calls.

### 默认时间源的时间标记基于内联类

Kotlin 1.7.0 improves the performance of time measurement functionality by changing the time marks returned
by `TimeSource.Monotonic` into inline value classes. This means that calling functions like `markNow()`, `elapsedNow()`
, `measureTime()`, and `measureTimedValue()` doesn't allocate wrapper classes for their `TimeMark` instances. Especially
when measuring a piece of code that is part of a hot path, this can help minimize the performance impact of the
measurement:

```kotlin
@OptIn(ExperimentalTime::class)
fun main() {
    val mark = TimeSource.Monotonic.markNow() // Returned `TimeMark` is inline class
    val elapsedDuration = mark.elapsedNow()
}
```

> This optimization is only available if the time source from which the `TimeMark` is obtained is statically known to
> be `TimeSource.Monotonic`.
>
{type="note"}

### Java Optional 的新实验性扩展函数

Kotlin 1.7.0 comes with new convenience functions that simplify working with `Optional` classes in Java. These new
functions can be used to unwrap and convert optional objects on the JVM and help make working with Java APIs more
concise.

The `getOrNull()`, `getOrDefault()`, and `getOrElse()` extension functions allow you to get the value of an `Optional`
if it's present. Otherwise, you get a default value, `null`, or a value returned by a function, respectively:

```kotlin
val presentOptional = Optional.of("I'm here!")

println(presentOptional.getOrNull())
// "I'm here!"

val absentOptional = Optional.empty<String>()

println(absentOptional.getOrNull())
// null
println(absentOptional.getOrDefault("Nobody here!"))
// "Nobody here!"
println(absentOptional.getOrElse {
    println("Optional was absent!")
    "Default value!"
})
// "Optional was absent!"
// "Default value!"
```

The `toList()`, `toSet()`, and `asSequence()` extension functions convert the value of a present `Optional` to a list,
set, or sequence, or return an empty collection otherwise. The `toCollection()` extension function appends
the `Optional` value to an already existing destination collection:

```kotlin
val presentOptional = Optional.of("I'm here!")
val absentOptional = Optional.empty<String>()
println(presentOptional.toList() + "," + absentOptional.toList())
// ["I'm here!"], []
println(presentOptional.toSet() + "," + absentOptional.toSet())
// ["I'm here!"], []
val myCollection = mutableListOf<String>()
absentOptional.toCollection(myCollection)
println(myCollection)
// []
presentOptional.toCollection(myCollection)
println(myCollection)
// ["I'm here!"]
val list = listOf(presentOptional, absentOptional).flatMap { it.asSequence() }
println(list)
// ["I'm here!"]
```

These extension functions are being introduced as Experimental in Kotlin 1.7.0. You can learn more about `Optional`
extensions in [this KEEP](https://github.com/Kotlin/KEEP/pull/291). As always, we welcome your feedback in
the [Kotlin issue tracker](https://kotl.in/issue).

### 在 JS 与 Native 平台支持命名捕获

Starting with Kotlin 1.7.0, named capturing groups are supported not only on the JVM, but on the JS and Native platforms
as well.

To give a name to a capturing group, use the (`?<name>group`) syntax in your regular expression. To get the text matched
by a group, call the newly introduced [`MatchGroupCollection.get()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/get.html)
function and pass the group name.

#### 按名称取回匹配分组的值

Consider this example for matching city coordinates. To get a collection of groups matched by the regular expression,
use [`groups`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/-match-result/groups.html). Compare retrieving a
group's contents by its number (index) and by its name using `value`:

```kotlin
fun main() {
    val regex = "\\b(?<city>[A-Za-z\\s]+),\\s(?<state>[A-Z]{2}):\\s(?<areaCode>[0-9]{3})\\b".toRegex()
    val input = "Coordinates: Austin, TX: 123"
    val match = regex.find(input)!!
    println(match.groups["city"]?.value) // "Austin" — by name
    println(match.groups[2]?.value) // "TX" — by number
}
```

#### 命名反向引用

You can now also use group names when backreferencing groups. Backreferences match the same text that was previously
matched by a capturing group. For this, use the `\k<name>` syntax in your regular expression:

```kotlin
fun backRef() {
    val regex = "(?<title>\\w+), yes \\k<title>".toRegex()
    val match = regex.find("Do you copy? Sir, yes Sir!")!!
    println(match.value) // "Sir, yes Sir"
    println(match.groups["title"]?.value) // "Sir"
}
```

#### 替换表达式中的命名分组

Named group references can be used with replacement expressions. Consider
the [`replace()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/-regex/replace.html) function that substitutes all
occurrences of the specified regular expression in the input with a replacement expression, and
the [`replaceFirst()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/-regex/replace-first.html) function that
swaps the first match only.

Occurrences of `${name}` in the replacement string are substituted with the subsequences corresponding to the captured
groups with the specified name. You can compare replacements in group references by name and index:

```kotlin
fun dateReplace() {
    val dateRegex = Regex("(?<dd>\\d{2})-(?<mm>\\d{2})-(?<yyyy>\\d{4})")
    val input = "Date of birth: 27-04-2022"
    println(dateRegex.replace(input, "\${yyyy}-\${mm}-\${dd}")) // "Date of birth: 2022-04-27" — by name
    println(dateRegex.replace(input, "\$3-\$2-\$1")) // "Date of birth: 2022-04-27" — by number
}
```

## Gradle

This release introduces new build reports, support for Gradle plugin variants, new statistics in kapt, and a lot more:

* [增量编译的新方式](#增量编译的新方式)
* [用于跟踪编译器性能的新版构建报告](#kotlin-编译器任务的构建报告)
* [Gradle 与 Android Gradle 插件最低支持版本的变更](#最低支持版本升级)
* [支持 Gradle 插件变体](#支持-gradle-插件变体)
* [Kotlin Gradle 插件 API 的更新](#kotlin-gradle-插件-api-的更新)
* [通过插件 API 提供 sam-with-receiver 插件](#通过插件-api-提供了-sam-with-receiver-插件)
* [编译任务的变更](#编译任务的变更)
* [kapt 中每个注解处理器生成文件的新版统计信息](#kapt-中每个注解处理器生成文件的统计信息)
* [弃用 kotlin.compiler.execution.strategy 系统属性](#弃用-kotlin-compiler-execution-strategy-系统属性)
* [删除弃用的选项、方法与插件](#删除弃用的选项方法与插件)

### 增量编译的新方式

> The new approach to incremental compilation is [Experimental](components-stability.md). It may be dropped or changed at any time.
> Opt-in is required (see the details below). We encourage you to use it only for evaluation purposes, and we would
> appreciate your feedback in [YouTrack](https://youtrack.jetbrains.com/issues/KT).
>
{type="warning"}

In Kotlin 1.7.0, we've reworked incremental compilation for cross-module changes. Now incremental compilation is also
supported for changes made inside dependent non-Kotlin modules, and it is compatible with
the [Gradle build cache](https://docs.gradle.org/current/userguide/build_cache.html). Support for compilation avoidance
has also been improved.

We expect you'll see the most significant benefit of the new approach if you use the build cache or frequently make
changes in non-Kotlin Gradle modules. Our tests for the Kotlin project on the `kotlin-gradle-plugin` module show an
improvement of greater than 80% for the changes after the cache hit.

To try this new approach, set the following option in your `gradle.properties`:

```properties
kotlin.incremental.useClasspathSnapshot=true
```

> The new approach to incremental compilation is currently available for the JVM backend in the Gradle build system
> only.
>
{type="note"}

Learn how the new approach to incremental compilation is implemented under the hood in
[this blog post](https://blog.jetbrains.com/kotlin/2022/07/a-new-approach-to-incremental-compilation-in-kotlin/).

Our plan is to stabilize this technology and add support for other backends (JS, for instance) and build systems. We'd
appreciate your reports in [YouTrack](https://youtrack.jetbrains.com/issues/KT) about any issues or strange behavior you
encounter in this compilation scheme. Thank you!

The Kotlin team is very grateful to [Ivan Gavrilovic](https://github.com/gavra0), [Hung Nguyen](https://github.com/hungvietnguyen),
[Cédric Champeau](https://github.com/melix), and other external contributors for their help.

### Kotlin 编译器任务的构建报告

> Kotlin build reports are [Experimental](components-stability.md). They may be dropped or changed at any time.
> Opt-in is required (see details below). Use them only for evaluation purposes. We appreciate your feedback on them
> in [YouTrack](https://youtrack.jetbrains.com/issues/KT).
>
{type="warning"}

Kotlin 1.7.0 introduces build reports that help track compiler performance. Reports contain the durations of different
compilation phases and reasons why compilation couldn't be incremental.

Build reports come in handy when you want to investigate issues with compiler tasks, for example:

* When the Gradle build takes too much time and you want to understand the root cause of the poor performance.
* When the compilation time for the same project differs, sometimes taking seconds, sometimes taking minutes.

To enable build reports, declare where to save the build report output in `gradle.properties`:

```properties
kotlin.build.report.output=file
```

The following values (and their combinations) are available:

* `file` saves build reports in a local file.
* `build_scan` saves build reports in the `custom values` section of the [build scan](https://scans.gradle.com/).

  > The Gradle Enterprise plugin limits the number of custom values and their length. In big projects, some values could
  be lost.
  >
  {type="note"}

* `http` posts build reports using HTTP(S). The POST method sends metrics in the JSON format. Data may change from version
  to version. You can see the current version of the sent data in the [Kotlin repository](https://github.com/JetBrains/kotlin/blob/master/libraries/tools/kotlin-gradle-plugin/src/common/kotlin/org/jetbrains/kotlin/gradle/plugin/statistics/CompileStatisticsData.kt).

There are two common cases that analyzing build reports for long-running compilations can help you resolve:

* The build wasn't incremental. Analyze the reasons and fix underlying problems.
* The build was incremental, but took too much time. Try to reorganize source files — split big files, save separate
  classes in different files, refactor large classes, declare top-level functions in different files, and so on.

Learn more about new build reports in [this blog post](https://blog.jetbrains.com/kotlin/2022/06/introducing-kotlin-build-reports/).

You are welcome to try using build reports in your infrastructure. If you have any feedback, encounter any issues, or
want to suggest improvements, please don't hesitate to report them in
our [issue tracker](https://youtrack.jetbrains.com/newIssue). Thank you!

### 最低支持版本升级

Starting with Kotlin 1.7.0, the minimum supported Gradle version is 6.7.1. We had
to [raise the version](https://youtrack.jetbrains.com/issue/KT-49733/Bump-minimal-supported-Gradle-version-to-6-7-1) to
support [Gradle plugin variants](#支持-gradle-插件变体) and the new Gradle API. In the future, we should
not have to raise the minimum supported version as often, thanks to the Gradle plugin variants feature.

Also, the minimal supported Android Gradle plugin version is now 3.6.4.

### 支持 Gradle 插件变体

Gradle 7.0 introduced a new feature for Gradle plugin authors
— [plugins with variants](https://docs.gradle.org/7.0/userguide/implementing_gradle_plugins.html#plugin-with-variants).
This feature makes it easier to add support for new Gradle features while maintaining compatibility for Gradle versions
below 7.1. Learn more about [variant selection in Gradle](https://docs.gradle.org/current/userguide/variant_model.html).

With Gradle plugin variants, we can ship different Kotlin Gradle plugin variants for different Gradle versions. The goal
is to support the base Kotlin compilation in the `main` variant, which corresponds to the oldest supported versions of
Gradle. Each variant will have implementations for Gradle features from a corresponding release. The latest variant will
support the widest Gradle feature set. With this approach, we can extend support for older Gradle versions with limited
functionality.

Currently, there are only two variants of the Kotlin Gradle plugin:

* `main` for Gradle versions 6.7.1–6.9.2
* `gradle70` for Gradle versions 7.0 and higher

In future Kotlin releases, we may add more.

To check which variant your build uses, enable
the [`--info` log level](https://docs.gradle.org/current/userguide/logging.html#sec:choosing_a_log_level) and find a
string in the output starting with `Using Kotlin Gradle plugin`, for example, `Using Kotlin Gradle plugin main variant`.

> Here are workarounds for some known issues with variant selection in Gradle:
> * [ResolutionStrategy in pluginManagement is not working for plugins with multivariants](https://github.com/gradle/gradle/issues/20545)
> * [Plugin variants are ignored when a plugin is added as the `buildSrc` common dependency](https://github.com/gradle/gradle/issues/20847)
>
{type="note"}

Leave your feedback
on [this YouTrack ticket](https://youtrack.jetbrains.com/issue/KT-49227/Support-Gradle-plugins-variants).

### Kotlin Gradle 插件 API 的更新

The Kotlin Gradle plugin API artifact has received several improvements:

* There are new interfaces for Kotlin/JVM and Kotlin/kapt tasks with user-configurable inputs.
* There is a new `KotlinBasePlugin` interface that all Kotlin plugins inherit from. Use this interface when you want to
  trigger some configuration action whenever any Kotlin Gradle plugin (JVM, JS, Multiplatform, Native, and other
  platforms) is applied:

  ```kotlin
  project.plugins.withType<org.jetbrains.kotlin.gradle.plugin.KotlinBasePlugin>() {
      // Configure your action here
  }
  ```
  You can leave your feedback about the `KotlinBasePlugin`
  in [this YouTrack ticket](https://youtrack.jetbrains.com/issue/KT-48008/Consider-offering-a-KotlinBasePlugin).

* We've laid the groundwork for the Android Gradle plugin to configure Kotlin compilation within itself, meaning you
  won't need to add the Kotlin Android Gradle plugin to your build.
  Follow [Android Gradle Plugin release announcements](https://developer.android.com/studio/releases/gradle-plugin) to
  learn about the added support and try it out!

### 通过插件 API 提供了 sam-with-receiver 插件

The [sam-with-receiver compiler plugin](sam-with-receiver-plugin.md) is now available via
the [Gradle plugins DSL](https://docs.gradle.org/current/userguide/plugins.html#sec:plugins_block):

```kotlin
plugins {
    id("org.jetbrains.kotlin.plugin.sam.with.receiver") version "$kotlin_version"
}
```

### 编译任务的变更

Compile tasks have received lots of changes in this release:

* Kotlin compile tasks no longer inherit the Gradle `AbstractCompile` task. They inherit only the `DefaultTask`.
* The `AbstractCompile` task has the `sourceCompatibility` and `targetCompatibility` inputs. Since the `AbstractCompile`
  task is no longer inherited, these inputs are no longer available in Kotlin users' scripts.
* The `SourceTask.stableSources` input is no longer available, and you should use the `sources` input. `setSource(...)`
  methods that are still available.
* All compile tasks now use the `libraries` input for a list of libraries required for compilation. The `KotlinCompile`
  task still has the deprecated Kotlin property `classpath`, which will be removed in future releases.
* Compile tasks still implement the `PatternFilterable` interface, which allows the filtering of Kotlin sources.
  The `sourceFilesExtensions` input was removed in favor of using `PatternFilterable` methods.
* The deprecated `Gradle destinationDir: File` output was replaced with the `destinationDirectory: DirectoryProperty`
  output.
* The Kotlin/Native `AbstractNativeCompile` task now inherits the `AbstractKotlinCompileTool` base class. This is an
  initial step toward integrating Kotlin/Native build tools into all the other tools.

Please leave your feedback in [this YouTrack ticket](https://youtrack.jetbrains.com/issue/KT-32805).

### kapt 中每个注解处理器生成文件的统计信息

The `kotlin-kapt` Gradle plugin
already [reports performance statistics for each processor](https://github.com/JetBrains/kotlin/pull/4280). Starting
with Kotlin 1.7.0, it can also report statistics on the number of generated files for each annotation processor.

This is useful to track if there are unused annotation processors as a part of the build. You can use the generated
report to find modules that trigger unnecessary annotation processors and update the modules to prevent that.

Enable the statistics in two steps:

* Set the `showProcessorStats` flag to `true` in your `build.gradle.kts`:

  ```kotlin
  kapt {
      showProcessorStats = true
  }

* Set the `kapt.verbose` Gradle property to `true` in your `gradle.properties`:
  ```properties
  kapt.verbose=true
  ```

> You can also enable verbose output via the [command line option `verbose`](kapt.md#using-in-cli).
>
{type="note"}

The statistics will appear in the logs with the `info` level. You'll see the `Annotation processor stats:` line followed
by statistics on the execution time of each annotation processor. After these lines, there will be
the `Generated files report:` line followed by statistics on the number of generated files for each annotation
processor. For example:

```text
[INFO] Annotation processor stats:
[INFO] org.mapstruct.ap.MappingProcessor: total: 290 ms, init: 1 ms, 3 round(s): 289 ms, 0 ms, 0 ms
[INFO] Generated files report:
[INFO] org.mapstruct.ap.MappingProcessor: total sources: 2, sources per round: 2, 0, 0
```

Please leave your feedback
in [this YouTrack ticket](https://youtrack.jetbrains.com/issue/KT-51132/KAPT-Support-reporting-the-number-of-generated-files-by-each-ann).

### 弃用 kotlin.compiler.execution.strategy 系统属性

Kotlin 1.6.20
introduced [new properties for defining a Kotlin compiler execution strategy](whatsnew1620.md#用于定义-kotlin-编译器执行策略的属性).
In Kotlin 1.7.0, a deprecation cycle has started for the old system property `kotlin.compiler.execution.strategy` in
favor of the new properties.

When using the `kotlin.compiler.execution.strategy` system property, you'll receive a warning. This property will be
deleted in future releases. To preserve the old behavior, replace the system property with the Gradle property of the
same name. You can do this in `gradle.properties`, for example:

```properties
kotlin.compiler.execution.strategy=out-of-process
```

You can also use the compile task property `compilerExecutionStrategy`. Learn more about this on
the [Gradle page](gradle.md#defining-kotlin-compiler-execution-strategy).

### 删除弃用的选项、方法与插件

#### 删除 useExperimentalAnnotation 方法

In Kotlin 1.7.0, we completed the deprecation cycle for the `useExperimentalAnnotation` Gradle method. Use `optIn()`
instead to opt in to using an API in a module.

For example, if your Gradle module is multiplatform:

```kotlin
sourceSets {
    all {
        languageSettings.optIn("org.mylibrary.OptInAnnotation")
    }
}
```

Learn more about [opt-in requirements](opt-in-requirements.md) in Kotlin.

#### 删除弃用的编译器选项

We've completed the deprecation cycle for several compiler options:

* The `kotlinOptions.jdkHome` compiler option was deprecated in 1.5.30 and has been removed in the current release.
  Gradle builds now fail if they contain this option. We encourage you to
  use [Java toolchains](whatsnew1530.md#支持-java-toolchains), which have been supported since Kotlin 1.5.30.
* The deprecated 'noStdlib' compiler option has also been removed. The Gradle plugin uses
  the `kotlin.stdlib.default.dependency=true` property to control whether the Kotlin standard library is present.

> The compiler arguments `-jdkHome` and `-no-stdlib` are still available.
>
{type="note"}

#### 删除弃用的插件

In Kotlin 1.4.0, the `kotlin2js` and `kotlin-dce-plugin` plugins were deprecated, and they have been removed in this
release. Instead of `kotlin2js`, use the new `org.jetbrains.kotlin.js` plugin. Dead code elimination (DCE) works when
the Kotlin/JS Gradle plugin is [properly configured](javascript-dce.md).

In Kotlin 1.6.0, we changed the deprecation level of the `KotlinGradleSubplugin` class to 'ERROR'. Developers used this
class for writing compiler plugins. In this
release, [this class has been removed](https://youtrack.jetbrains.com/issue/KT-48831/). Use
the `KotlinCompilerPluginSupportPlugin` class instead.

> The best practice is to use Kotlin plugins with versions 1.7.0 and higher throughout your project.
>
{type="tip"}

#### 删除弃用的协程 DSL 选项与属性

We removed the deprecated `kotlin.experimental.coroutines` Gradle DSL option and the `kotlin.coroutines` property used
in `gradle.properties`. Now you can just use _[suspending functions](coroutines-basics.md#extract-function-refactoring)_
or [add the `kotlinx.coroutines` dependency](gradle.md#set-a-dependency-on-a-kotlinx-library) to your build
script.

Learn more about coroutines in the [Coroutines guide](coroutines-guide.md).

#### 删除工具链扩展方法中的类型转换

Before Kotlin 1.7.0, you had to do the type cast into the `JavaToolchainSpec` class when configuring the Gradle
toolchain with Kotlin DSL:

```kotlin
kotlin {
    jvmToolchain {
        (this as JavaToolchainSpec).languageVersion.set(JavaLanguageVersion.of(<MAJOR_JDK_VERSION>)
    }
}
```

Now, you can omit the `(this as JavaToolchainSpec)` part:

```kotlin
kotlin {
    jvmToolchain {
        languageVersion.set(JavaLanguageVersion.of(<MAJOR_JDK_VERSION>)
    }
}
```

## 迁移到 Kotlin 1.7.0

### 安装 Kotlin 1.7.0

IntelliJ IDEA 2022.1 and Android Studio Chipmunk (212) automatically suggest updating the Kotlin plugin to 1.7.0.

> For IntelliJ IDEA 2022.2, and Android Studio Dolphin (213) or Android Studio Electric Eel (221), the Kotlin plugin 1.7.0 will be delivered with upcoming IntelliJ IDEA and Android Studios updates.
> 
{type="note"}

The new command-line compiler is available for download on the [GitHub release page](https://github.com/JetBrains/kotlin/releases/tag/v1.7.0).

### 使用 Kotlin 1.7.0 迁移既有项目或启动新项目

* To migrate existing projects to Kotlin 1.7.0, change the Kotlin version to `1.7.0` and reimport your Gradle or Maven
project. [Learn how to update to Kotlin 1.7.0](releases.md#更新到新版本).

* To start a new project with Kotlin 1.7.0, update the Kotlin plugin and run the Project Wizard from **File** \| **New** \|
**Project**.

### Kotlin 1.7.0 的兼容性指南

Kotlin 1.7.0 is a [feature release](kotlin-evolution.md#特性发布与增量发布) and can, therefore, bring changes that are incompatible with your code written for earlier versions of the language.
Find the detailed list of such changes in the [Compatibility guide for Kotlin 1.7.0](compatibility-guide-17.md).