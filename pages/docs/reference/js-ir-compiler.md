---
type: doc
layout: reference
category: "JavaScript"
title: "使用 IR 编译器"
---
# 使用 Kotlin/JS IR 编译器

> 从 Kotlin 1.4.0 开始，Kotlin/JS IR 编译器具有 _[Alpha](evolution/components-stability.html)_ 稳定性级别。欢迎使用 IR 编译器后端，但是本文档中描述的所有功能、语言与工具特性都可能在将来的 Kotlin 版本中改变。
{:.note}

Kotlin/JS IR 编译器后端是围绕 Kotlin/JS 进行创新的主要焦点，并为该技术的发展铺平了道路。

Kotlin/JS IR 编译器后端没有直接从 Kotlin 源代码生成 JavaScript 代码，而是利用了一种新途径。首先将 Kotlin 源代码转换为 [Kotlin 中间表示（IR）](whatsnew14.html#统一的后端与可扩展性)，然后将其编译为 JavaScript。对于 Kotlin/JS，这可以进行积极的优化，并可以改善以前的编译器中存在的痛点，例如生成的代码大小（通过消除无效代码）以及 JavaScript 与 TypeScript 生态系统的互操作性。

从 Kotlin 1.4.0 开始，可以通过 Kotlin/JS Gradle 插件使用 IR 编译器后端。要在项目中启用它，请将编译器类型传递给 Gradle 构建脚本中的 `js` 函数：

<!--suppress ALL -->
> Groovy DSL

```groovy
kotlin {
    js(IR) { // 或：LEGACY、BOTH
        // . . .
        binaries.executable()
    }
}
```



- `IR` 使用 Kotlin/JS 的新 IR 编译器后端。
- `LEGACY` 使用默认编译器后端。
- `BOTH` 使用新的 IR 编译器以及默认的编译器后端编译项目。这主要用于创作与两个后端兼容的库，请参见[此处](#authoring-libraries-for-the-ir-compiler-with-backwards-compatibility)。

还可以使用键值 `kotlin.js.compiler=ir` 在 `gradle.properties` 文件中设置编译器类型。（但是，`build.gradle(.kts)` 中的任何设置都会覆盖此行为）。

## 忽略编译错误

>在 Kotlin 1.4.20 中，_忽略编译错误_ 模式是[实验性的](evolution/components-stability.html)。
>其行为与接口将来可能会更改。
{:.note}

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

> Kotlin DSL

```kotlin
kotlin {
   js(IR) {
       compilations.all {
           compileKotlinTask.kotlinOptions.freeCompilerArgs += listOf("-Xerror-tolerance-policy=SYNTAX")
       }
   }
}
```


## IR 编译器的当前限制

新的 IR 编译器后端的主要变化是与默认后端 **没有二进制兼容性**。Kotlin/JS 的两个后端之间缺乏这种兼容性，这意味着使用新的 IR 编译器后端创建的库无法从默认后端使用，反之亦然。

如果要为项目使用 IR 编译器后端，则需要 **将所有 Kotlin 依赖项更新为支持该新后端的版本**。由 JetBrains 针对 Kotlin/JS 发布的针对 Kotlin 1.4+ 的库已经包含了与新的 IR 编译器后端一起使用所需的所有构件。

**可能库开发者** 希望提供与当前编译器后端以及新的 IR 编译器后端的兼容性，请另外查看[“为 IR 编译器编写库”](#authoring-libraries-for-the-ir-compiler-with-backwards-compatibility)部分。

与默认后端相比，IR 编译器后端也存在一些差异。在尝试新的后端时，最好注意这些可能的缺陷。
- 当前，IR 后端 **无法为 Kotlin 代码生成源映射**。可以[在 YouTrack](https://youtrack.jetbrains.com/issue/KT-39447) 上跟踪进度。
- 一些 **依赖默认后端特定特性的库**，例如 `kotlin-wrappers`，可能会显示一些问题。可以[在 YouTrack](https://youtrack.jetbrains.com/issue/KT-40525) 上跟踪调查与进度。
- 默认情况下，IR 后端根本 **不会使 Kotlin 声明可用于 JavaScript**。要使 Kotlin 声明对 JavaScript 可见，**必须使用** [`@JsExport`](js-to-kotlin-interop.html#jsexport-annotation) 对其进行注解。

## 预览：TypeScript 声明文件（d.ts）的生成
Kotlin/JS IR 编译器能够从 Kotlin 代码生成 TypeScript 定义。在混合应用程序上工作时，JavaScript 工具与 IDE 可以使用这些定义来提供自动补全功能、支持静态分析器，并使在 JavaScript 与 TypeScript 项目中更容易包含 Kotlin 代码。
在产生可执行文件（`binaries.executable()`）的项目中，标有 [`@JsExport`](js-to-kotlin-interop.html#jsexport-annotation) 的顶级声明将生成一个 `.d.ts` 文件，其中包含导出的 Kotlin 声明的 TypeScript 定义。
在 Kotlin 1.4 中，可以在 `build/js/packages/<package_name>/kotlin` 中找到这些声明以及相应的未打包 Web 的 JavaScript 代码。

TypeScript 声明文件的生成是 IR 编译器独有的功能，并且正在积极开发中。如果遇到任何问题，请将其提交给 Kotlin [问题跟踪器](https://youtrack.jetbrains.com/issues?q=%23%7BKJS:%20d.ts%20generation%7D)，或对已提交问题进行投票。

## 具有向后兼容性的 IR 编译器开发库

对于库维护者，希望提供与默认后端以及新的 IR 编译器后端的兼容性，那么可以使用编译器选择的设置，该设置可为两个后端创建构件，从而保持与以下版本的兼容性。为现有的用户以及下一代 Kotlin 编译器提供支持。可以使用 `gradle.properties` 文件中的 `kotlin.js.compiler=both` 设置打开这种所谓的 `both` 模式，也可以将其设置为 `build.gradle(.kts)` 文件内 `js` 块内特定于项目的选项之一：

```groovy
kotlin {
    js(BOTH) {
        // . . .
    }
}
```

在 `both` 模式下，从源构建库时都使用 IR 编译器后端与默认编译器后端（因此命名）。这意味着将同时生成带有 Kotlin IR 的 `klib` 文件以及默认编译器的 `jar` 文件。在相同的 Maven 坐标下发布时，Gradle 将根据用例自动选择合适的构件——`js` 用于旧编译器，`klib` 用于新编译器。这使得能够为使用两种编译器后端之一的项目编译与发布库。
