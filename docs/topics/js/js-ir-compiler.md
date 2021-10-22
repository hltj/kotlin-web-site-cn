[//]: # (title: Kotlin/JS IR 编译器)

> The Kotlin/JS IR compiler is in [Beta](components-stability.md). It is almost stable, but migration steps may be required
> in the future. We'll do our best to minimize any changes you have to make.
>
{type="warning"}

Kotlin/JS IR 编译器后端是围绕 Kotlin/JS 进行创新的主要焦点，并为该技术的发展铺平了道路
。 

Kotlin/JS IR 编译器后端没有直接从 Kotlin 源代码生成 JavaScript 代码，而是利用了<!--
-->一种新途径。首先将 Kotlin 源代码转换为
[Kotlin 中间表示（IR）](whatsnew14.md#统一的后端与可扩展性)，
然后将其编译为 JavaScript。
对于 Kotlin/JS，这可以进行积极的优化，并可以<!--
-->改善以前的编译器中存在的痛点，例如生成的代码大小（通过消除无效代码）以及 JavaScript 与 TypeScript 生态系统的互操作性。

从 Kotlin 1.4.0 开始，可以通过 Kotlin/JS Gradle 插件使用 IR 编译器后端。要在项目中启用它，
请将编译器类型传递给 Gradle 构建脚本中的 `js` 函数：

<!--suppress ALL -->

```groovy
kotlin {
    js(IR) { // 或：LEGACY、BOTH
        // ...
    }
    binaries.executable() // not applicable to BOTH, see details below
}
```

- `IR` 使用 Kotlin/JS 的新 IR 编译器后端。
- `LEGACY` 使用默认编译器后端。
- `BOTH` 使用新的 IR 编译器以及默认的编译器后端编译项目。这个模式用于[创作与两个后端兼容的库](#为-ir-编译器创作具有向后兼容性的库)。

还可以使用键值 `kotlin.js.compiler=ir` 在 `gradle.properties` 文件中设置编译器类型。
但是，`build.gradle(.kts)` 中的任何设置都会覆盖此行为。

## 忽略编译错误

>_Ignore compilation errors_ mode is [Experimental](components-stability.md). It may be dropped or changed at any time.
> Opt-in is required (see the details below), and you should use it only for evaluation purposes. We would appreciate your feedback on it in [YouTrack](https://youtrack.jetbrains.com/issues/KT).
>
{type="warning"}

Kotlin/JS IR 编译器提供了默认后端中不可用的新编译模式——_忽略编译错误_。
在这种模式下，即使其代码包含错误，也可以试运行应用程序。
例如，当执行复杂的重构或在系统的某个部分上进行工作时，
而该部分与另一部分中的编译错误完全无关。

使用这种新的编译器模式，编译器会忽略所有有毛病的代码。
因此，可以试运行应用程序并尝试没毛病的部分代码。
如果试运行到在编译过程中出了毛病的代码，那么会得到运行时异常。

在两个容忍策略之间选择，以忽略代码中的编译错误：
- `SEMANTIC`：编译器将接受语法上正确但语义上没有意义的代码。
  例如，为字符串变量赋值一个数字（类型不匹配）。
- `SYNTAX`：编译器将接受任何代码，即使其中包含语法错误。
  无论编写什么内容，编译器仍尝试生成可运行的可执行文件。
    
作为实验特性，忽略编译错误需要选择加入。
要启用此模式，请添加 `-Xerror-tolerance-policy={SEMANTIC|SYNTAX}` 编译器选项：

```kotlin
kotlin {
   js(IR) {
       compilations.all {
           compileKotlinTask.kotlinOptions.freeCompilerArgs += listOf("-Xerror-tolerance-policy=SYNTAX")
       }
   }
}
```

## Lazy initialization of top-level properties

> Lazy initialization of top-level properties is [Experimental](components-stability.md). It may be dropped or changed at any time.
> Opt-in is required (see the details below), and you should use it only for evaluation purposes. We would appreciate your feedback on it in [YouTrack](https://youtrack.jetbrains.com/issue/KT-44320).
>
{type="warning"}

For better application startup performance, the Kotlin/JS IR compiler offers an option to initialize top-level properties
lazily. This way, the application loads without initializing all the top-level properties used in its code. It initializes
only the ones needed at startup; other properties receive their values later when the code that uses them actually runs. 

As an experimental feature, lazy initialization of top-level properties requires an opt-in. To use the lazy initialization
of top-level properties, add the `-Xir-property-lazy-initialization` option when compiling the code with the JS IR compiler:

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
tasks.withType<Kotlin2JsCompile> {
   kotlinOptions {
     freeCompilerArgs += "-Xir-property-lazy-initialization"
   }
}
```

</tab>
<tab title="Groovy" group-key="groovy">
    
```groovy
tasks.withType(Kotlin2JsCompile) {
   kotlinOptions {
     freeCompilerArgs += "-Xir-property-lazy-initialization"
   }
}
```

</tab>
</tabs>

## 预览：TypeScript 声明文件（d.ts）的生成

> The generation of TypeScript declaration files (`d.ts`) is [Experimental](components-stability.md). It may be dropped or changed at any time.
> Opt-in is required (see the details below), and you should use it only for evaluation purposes. We would appreciate your feedback on it in [YouTrack](https://youtrack.jetbrains.com/issues?q=%23%7BKJS:%20d.ts%20generation%7D).
>
{type="warning"}

Kotlin/JS IR 编译器能够从 Kotlin 代码生成 TypeScript 定义。在混合应用程序上工作时，
JavaScript 工具与 IDE 可以使用这些定义来提供自动补全功能、支持静态分析器，
并使在 JavaScript 与 TypeScript 项目中更容易包含 Kotlin 代码。

在产生可执行文件（`binaries.executable()`）的项目中，标有 [`@JsExport`](js-to-kotlin-interop.md#jsexport-注解)
的顶级声明将生成一个 `.d.ts` 文件，
其中包含导出的 Kotlin 声明的 TypeScript 定义。
可以在 `build/js/packages/<package_name>/kotlin` 中找到这些声明以及相应的<!--
-->未打包 Web 的 JavaScript 代码。

## IR 编译器的当前限制

新的 IR 编译器后端的主要变化是与默认后端 **没有二进制兼容性**。
Kotlin/JS 的两个后端之间缺乏这种兼容性，这意味着使用新的 IR 编译器后端创建的库<!--
-->无法在默认后端使用，反之亦然。

如果要为项目使用 IR 编译器后端，则需要 **将所有 Kotlin 依赖项更新为<!--
-->支持该新后端的版本**。由 JetBrains 针对 Kotlin/JS 发布的针对 Kotlin 1.4+ 的库已经包含了<!--
-->与新的 IR 编译器后端一起使用所需的所有构件。

**可能库开发者** 希望提供与当前编译器后端以及新的 IR
编译器后端的兼容性，请另外查看[“为 IR 编译器编写库”相关部分](#为-ir-编译器创作具有向后兼容性的库)
部分。

与默认后端相比，IR 编译器后端也存在一些差异。在尝试新的后端时，
最好注意这些可能的缺陷。

- 一些 **依赖默认后端特定特性的库**，例如 `kotlin-wrappers`，可能会显示一些问题。可以[在 YouTrack](https://youtrack.jetbrains.com/issue/KT-40525) 上跟踪调查与进度。
- 默认情况下，IR 后端根本 **不会使 Kotlin 声明可用于 JavaScript**。要使 Kotlin 声明对 JavaScript 可见，**必须使用** [`@JsExport`](js-to-kotlin-interop.md#jsexport-注解) 对其进行注解。

## Migrating existing projects to the IR compiler

Due to significant differences between the two Kotlin/JS compilers, making your Kotlin/JS code work with the IR compiler
may require some adjustments. Learn how to migrate existing Kotlin/JS projects to the IR compiler in the [Kotlin/JS IR
compiler migration guide](js-ir-migration.md).

## 为 IR 编译器创作具有向后兼容性的库

对于库维护者，希望提供与默认后端以及新的 IR
编译器后端的兼容性，那么可以使用编译器选择的设置，该设置可为两个后端创建构件，
从而保持与以下版本的兼容性。为现有的用户以及下一代 Kotlin 编译器提供支持。
可以使用 `gradle.properties` 文件中的 `kotlin.js.compiler=both` 设置打开这种所谓的 `both` 模式，
也可以将其设置为 `build.gradle(.kts)` 文件内 `js` 块内特定于项目的选项之一：

```groovy
kotlin {
    js(BOTH) {
        // ...
    }
}
```

When in `both` mode, the IR compiler backend and default compiler backend are both used when building a library from your
sources (hence the name). This means that both `klib` files with Kotlin IR as well as `jar` files for the default compiler
will be generated. When published under the same Maven coordinate, Gradle will automatically choose the right artifact
depending on the use case – `js` for the old compiler, `klib` for the new one. This enables you to compile and publish
your library for projects that are using either of the two compiler backends.
