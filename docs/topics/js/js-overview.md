[//]: # (title: Kotlin 用于 JavaScript 开发)

Kotlin/JS 提供了转换 Kotlin 代码、Kotlin 标准库的能力，并且兼容 JavaScript
的任何依赖项。Kotlin/JS 的当前实现以 [ES5](https://www.ecma-international.org/ecma-262/5.1/) 为目标。

使用 Kotlin/JS 的推荐方法是通过 `kotlin.js` 与 `kotlin.multiplatform` Gradle 插件。它们提供了一种<!--
-->集中与便捷的方式来设置与控制以 JavaScript 为目标的 Kotlin 项目。
这包括基本特性，例如控制应用程序的捆绑，直接从 npm 添加 JavaScript 依赖项等等。要获得<!--
-->可用选项的概述，请查看[搭建 Kotlin/JS 项目](js-project-setup.md)文档。

## Kotlin/JS 的使用场景

有很多可以使用 Kotlin/JS 的方法。为了给出一些启发，这里列出了可以使用 Kotlin/JS
的场景的一个不完全的清单。

* **使用 Kotlin/JS 编写 Web 前端应用程序**
    * Kotlin/JS 允许以类型安全的方式 **利用功能强大的浏览器与 Web API**。创建、修改文档对象模型（DOM）中的元素<!--
    -->并与之交互，使用 Kotlin 代码控制 `canvas` 或 WebGL 组件的呈现，
    并享受对现代浏览器支持的更多功能的访问。
    * 使用 JetBrains 提供的 [`kotlin-wrappers`](https://github.com/JetBrains/kotlin-wrappers)
    **用 Kotlin/JS 编写完整的，类型安全的 React 应用程序**，它为最流行的 JavaScript 框架之一提供方便的抽象与深度集成。
    `kotlin-wrappers` 还为许多类似技术（例如
    `react-redux`、`react-router` 或 `styled-components`）提供支持。
    与 JavaScript 生态系统的互操作性还意味着可以使用第三方 React 组件与组件库。
    * 使用 **[Kotlin/JS 框架](#kotlin-js-框架)**，充分利用 Kotlin 相关概念、其表现力<!--
    -->与简洁性（例如 [kvision](https://kvision.io) 或 [fritz2](https://www.fritz2.dev/)）。

* **使用 Kotlin/JS 编写服务器端与无服务器应用程序**
    * Kotlin/JS 提供的 Node.js 目标能够创建**在服务器上运行**或在<!--
    -->**无服务器基础架构上执行**的应用程序。可以享受与其他在 JavaScript
    运行时中执行的应用程序相同的优势，例如**更快的启动速度**与**更少的内存占用**。使用 [`kotlinx-nodejs`](https://github.com/Kotlin/kotlinx-nodejs)，
    可以直接从 Kotlin 代码中对 [Node.js API](https://nodejs.org/docs/latest/api/) 进行类型安全的访问。

*  **使用 Kotlin 的[多平台](multiplatform.md)项目与其他 Kotlin 目标共享代码**
    * 使用 `multiplatform` 多平台 Gradle 插件时，也可以访问所有 Kotlin/JS 功能。
    * 如果有用 Kotlin 编写的后端，那么可以与用
    Kotlin/JS 编写的前端**共享公共代码**，例如数据模型或逻辑验证，从而能够**编写与维护全栈 Web 应用程序**。
    * 还可以**在 Web 界面与移动应用之间共享业务逻辑**（Android 与 iOS），并避免<!--
    -->重复实现常见的功能，例如围绕 REST API 端点提供抽象，用户身份验证<!--
    -->或者领域模型。

* **创建用于 JavaScript 与 TypeScript 的库**
    * 也不必用 Kotlin/JS 编写整个应用程序——可以**从 Kotlin 代码生成库**，
    这些库可以在 JavaScript 或 TypeScript 编写的任何代码库中作为模块使用，而与所使用的<!--
    -->其他框架或技术无关。这种**创建混合应用程序**的方法可以利用<!--
    -->个人与团队在 Web 开发方面已经具备的能力，同时**减少重复的工作量**，
    并使 Web 目标与应用程序的其他目标平台保持一致变得更加容易。

当然，这并不是如何充分利用 Kotlin/JS 的完整列表，仅是精选的案例。
请尝试这些用例的组合，并找出最适合项目的方案。

无论具体用例如何，Kotlin/JS 项目都可以使用兼容**Kotlin 生态系统中的库**，
以及第三方的**JavaScript 与 TypeScript 生态系统中的库**。如果要在 Kotlin 代码中使用后者，
可以提供自己的类型安全包装器、使用社区维护的包装器， 也可以让 [Dukat](js-external-declarations-with-dukat.md)
自动生成 Kotlin 声明。使用 Kotlin/JS 专有的[动态类型](dynamic-type.md)可以放宽 Kotlin
的类型系统的约束，从而允许跳过创建详细的库包装器——
以类型安全为代价。

Kotlin/JS 还与最常见的模块系统兼容：UMD、CommonJS 与 AMD。能够[生产与使用模块](js-modules.md)<!--
-->意味着能够以结构化的方式与 JavaScript 生态系统进行交互。

## Kotlin/JS 框架

Modern web development benefits significantly from frameworks that simplify building web applications.
Here are examples of popular web frameworks for Kotlin/JS written by different authors:

### KVision

_KVision_ is an object-oriented web framework that makes it possible to write applications in Kotlin/JS with ready-to-use components
that can be used as building blocks for your application’s user interface. You can use both reactive and imperative programming
models to build your frontend, use connectors for Ktor, Spring Boot, and other frameworks to integrate it with your server-side
applications, and share code using [Kotlin Multiplatform](multiplatform.md).

Visit [https://kvision.io](https://kvision.io) for documentation, tutorials, and examples.

For updates and discussions about the framework, join [#kvision](https://kotlinlang.slack.com/messages/kvision) and
[#javascript](https://kotlinlang.slack.com/archives/C0B8L3U69) channels in the [Kotlin Slack](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up).

### fritz2

_fritz2_ is a standalone framework for building reactive web user interfaces. It provides its own type-safe DSL for building
and rendering HTML elements, and it makes use of Kotlin’s coroutines and flows to express components and their data bindings.
It provides state management, validation, routing, and more out of the box, and integrates with Kotlin Multiplatform projects.

Visit [https://www.fritz2.dev](https://www.fritz2.dev) for documentation, tutorials, and examples.

For updates and discussions about the framework, join the [#fritz2](https://kotlinlang.slack.com/messages/fritz2) and
[#javascript](https://kotlinlang.slack.com/archives/C0B8L3U69) channels in the [Kotlin Slack](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up).

### Doodle

_Doodle_ is a vector-based UI framework for Kotlin/JS. Doodle applications use the browser’s graphics capabilities to draw
user interfaces instead of relying on DOM, CSS, or Javascript. By using this approach, Doodle gives you precise control
over the rendering of arbitrary UI elements, vector shapes, gradients, and custom visualizations.

Visit [https://nacular.github.io/doodle/](https://nacular.github.io/doodle/) for documentation, tutorials, and examples.

For updates and discussions about the framework, join [#doodle](https://kotlinlang.slack.com/messages/doodle) and
[#javascript](https://kotlinlang.slack.com/archives/C0B8L3U69) channels in the [Kotlin Slack](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up).

### Compose for Web

_Compose for Web_, a part of Compose Multiplatform brings [Google's Jetpack Compose UI toolkit](https://developer.android.com/jetpack/compose)
to your browser. It allows you to build reactive web user interfaces using the concepts introduced by Jetpack Compose.
It provides a DOM API to describe your website, as well as an experimental set of multiplatform layout primitives.
Compose for Web also gives you the option to share parts of your UI code and logic across Android, desktop, and web.

Compose for Web is in [Alpha](components-stability.md), which means it hasn't reached the final shape yet, but you can
already implement a proof-of-concept for your production applications.

You can find more information about Compose Multiplatform on its [landing page](https://www.jetbrains.com/lp/compose-mpp/).

Join the [#compose-web](https://kotlinlang.slack.com/archives/C01F2HV7868) channel on [Kotlin Slack](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up)
to discuss Compose for Web, or [#compose](https://kotlinlang.slack.com/archives/CJLTWPH7S) for general Compose Multiplatform discussions.

## Kotlin/JS 今天与明天

**想进一步了解 Kotlin/JS 吗？**

在这个视频中（[YouTube](https://www.youtube.com/watch?v=fZUL8_kgHXg)、[bilibili](https://player.bilibili.com/player.html?aid=926746622&bvid=BV1FT4y1L77i&cid=223227283&page=1)），Kotlin 开发者布道师 Sebastian Aigner 将为你解释 Kotlin/JS
的主要优点、分享一些技巧与使用场景，并介绍 Kotlin/JS 的计划与即将发布的特性。

<iframe width="560" height="315" src="https://player.bilibili.com/player.html?aid=926746622&bvid=BV1FT4y1L77i&cid=223227283&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"></iframe><br />
<iframe width="560" height="315" src="https://www.youtube.com/embed/fZUL8_kgHXg" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe><br />

## Kotlin/JS 入门

如果不熟悉 Kotlin，那么第一步最好是熟悉该语言的[基本语法](basic-syntax.md)。

如需开始将 Kotlin 用于 JavaScript，请参考[搭建 Kotlin/JS 项目](js-project-setup.md)。 You can also
pick a [hands-on](#kotlin-js-动手实践实验室) lab to work through or check out the list of [Kotlin/JS sample projects](#kotlin-js-样例项目)
for inspiration. They contain useful snippets and patterns and can serve as nice jump-off points for your own projects.

## Kotlin/JS 动手实践实验室

* [Building Web Applications with React and Kotlin/JS](https://play.kotlinlang.org/hands-on/Building%20Web%20Applications%20with%20React%20and%20Kotlin%20JS/01_Introduction)
guides you through the process of building a simple web application using the React framework, shows how a type-safe Kotlin
DSL for HTML makes it easy to build reactive DOM elements, and illustrates how to use third-party React components and
obtain information from APIs, all while writing the whole application logic in pure Kotlin/JS.

* [Building a Full Stack Web App with Kotlin Multiplatform](https://play.kotlinlang.org/hands-on/Full%20Stack%20Web%20App%20with%20Kotlin%20Multiplatform/01_Introduction)
teaches the concepts behind building an application that targets Kotlin/JVM and Kotlin/JS by building a client-server
application that makes use of shared code, serialization, and other multiplatform paradigms. It also provides a brief
introduction to working with Ktor both as a server- and client-side framework.

## Kotlin/JS 样例项目

* [Full-stack Spring collaborative to-do list](https://github.com/Kotlin/full-stack-spring-collaborative-todo-list-sample)
shows how to create a to-do list for collaborative work using `kotlin-multiplatform` with JS and JVM targets, Spring
for the backend, Kotlin/JS with React for the frontend, and RSocket.
* [Kotlin/JS and React Redux to-do list](https://github.com/Kotlin/react-redux-js-ir-todo-list-sample) implements
the React Redux to-do list using JS libraries (`react`, `react-dom`, `react-router`, `redux`, and `react-redux`)
from npm and Webpack to bundle, minify, and run the project.
* [Full-stack demo application](https://github.com/Kotlin/full-stack-web-jetbrains-night-sample) guides you through
the process of building an app with a feed containing user-generated posts and comments. All data is stubbed by
the fakeJSON and JSON Placeholder services.

## 新的 Kotlin/JS IR 编译器

[新的 Kotlin/JS IR 编译器](js-ir-compiler.md)（当前稳定性：[Beta](components-stability.md)）
相对于当前的默认编译器进行了许多改进。例如，
通过消除死代码来减小生成的可执行文件的体积，并使与 JavaScript 生态系统及其工具的互操作更加流畅。
通过从 Kotlin 代码生成 TypeScript 声明文件（d.ts），新的编译器使创建混合 TypeScript 与 Kotlin 代码的“混合”
应用程序变得更加容易，并利用 Kotlin 多平台代码共享功能。

如需了解有关新 Kotlin/JS IR 编译器中可用特性的更多信息，以及如何在项目中尝试使用它，请访问其
[Kotlin/JS IR compiler documentation page](js-ir-compiler.md) and the [migration guide](js-ir-migration.md).

## 加入 Kotlin/JS 社区

还可以在官方 [Kotlin Slack](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up) 中加入 [#javascript](https://kotlinlang.slack.com/archives/C0B8L3U69) 频道，
同社区与团队交谈。