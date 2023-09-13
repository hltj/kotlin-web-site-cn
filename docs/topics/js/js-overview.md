[//]: # (title: Kotlin 用于 JavaScript 开发)

Kotlin/JS 提供了转换 Kotlin 代码、Kotlin 标准库的能力，并且兼容 JavaScript
的任何依赖项。Kotlin/JS 的当前实现以 [ES5](https://www.ecma-international.org/ecma-262/5.1/) 为目标。

使用 Kotlin/JS 的推荐方法是通过 `kotlin.multiplatform` Gradle 插件。它提供了一种集中且便捷的方式来<!--
-->设置与控制以 JavaScript 为目标的 Kotlin 项目。 这包括基本特性，
例如控制应用程序的捆绑，直接从 npm 添加 JavaScript 依赖项等等。要获得<!--
-->可用选项的概述，请查看[搭建 Kotlin/JS 项目](js-project-setup.md)。

## Kotlin/JS IR 编译器

[Kotlin/JS IR 编译器](js-ir-compiler.md)相对于旧版默认编译器进行了许多改进。
例如，通过消除死代码来减小生成的可执行文件的体积，
并提供了与 JavaScript 生态系统及其工具更加流畅的互操作性。

> 自 Kotlin 1.8.0 版起，旧编译器已弃用。
>
{type="note"}

通过从 Kotlin 代码生成 TypeScript 声明文件（`d.ts`），IR 编译器使创建混合 TypeScript 与 Kotlin 代码的“混合”
应用程序变得更加容易，并利用 Kotlin 多平台代码共享功能。

如需了解关于 Kotlin/JS IR 编译器中可用特性的更多信息，以及如何在项目中尝试使用它，请访问
[Kotlin/JS IR 编译器文档页](js-ir-compiler.md)及其[迁移指南](js-ir-migration.md)。

## Kotlin/JS 框架

Modern web development benefits significantly from frameworks that simplify building web applications.
Here are a few examples of popular web frameworks for Kotlin/JS written by different authors:

### KVision

_KVision_ is an object-oriented web framework that makes it possible to write applications in Kotlin/JS with ready-to-use components
that can be used as building blocks for your application's user interface. You can use both reactive and imperative programming
models to build your frontend, use connectors for Ktor, Spring Boot, and other frameworks to integrate it with your server-side
applications, and share code using [Kotlin Multiplatform](multiplatform.md).

[Visit KVision site](https://kvision.io) for documentation, tutorials, and examples.

For updates and discussions about the framework, join the [#kvision](https://kotlinlang.slack.com/messages/kvision) and
[#javascript](https://kotlinlang.slack.com/archives/C0B8L3U69) channels in the [Kotlin Slack](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up).

### fritz2

_fritz2_ is a standalone framework for building reactive web user interfaces. It provides its own type-safe DSL for building
and rendering HTML elements, and it makes use of Kotlin's coroutines and flows to express components and their data bindings.
It provides state management, validation, routing, and more out of the box, and integrates with Kotlin Multiplatform projects.

[Visit fritz2 site](https://www.fritz2.dev) for documentation, tutorials, and examples.

For updates and discussions about the framework, join the [#fritz2](https://kotlinlang.slack.com/messages/fritz2) and
[#javascript](https://kotlinlang.slack.com/archives/C0B8L3U69) channels in the [Kotlin Slack](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up).

### Doodle

_Doodle_ is a vector-based UI framework for Kotlin/JS. Doodle applications use the browser's graphics capabilities to draw
user interfaces instead of relying on DOM, CSS, or Javascript. By using this approach, Doodle gives you precise control
over the rendering of arbitrary UI elements, vector shapes, gradients, and custom visualizations.

[Visit Doodle site](https://nacular.github.io/doodle/) for documentation, tutorials, and examples.

For updates and discussions about the framework, join the [#doodle](https://kotlinlang.slack.com/messages/doodle) and
[#javascript](https://kotlinlang.slack.com/archives/C0B8L3U69) channels in the [Kotlin Slack](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up).

## 加入 Kotlin/JS 社区

可以在官方 [Kotlin Slack](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up) 中加入 [#javascript](https://kotlinlang.slack.com/archives/C0B8L3U69) 频道，
同社区与团队交谈。