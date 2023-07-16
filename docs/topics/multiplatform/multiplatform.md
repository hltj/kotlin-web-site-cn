[//]: # (title: Kotlin 多平台)
[//]: # (description: Kotlin Multiplatform allows creating cross-platform apps for desktop, web, and mobile devices.
Share application logic while keeping native user experience.)

> Kotlin 多平台处于 [Beta](components-stability.md) 阶段。它几乎是稳定的，但是将来可能需要迁移步骤。
> 我们会尽最大努力来减少开发者需要做的变更。
>
{type="note"}

Kotlin 多平台技术旨在简化跨平台项目的开发。 它减少了为<!--
-->[不同平台](#kotlin-多平台使用场景)编写与维护相同代码所花的时间，
同时保留了原生编程的灵活性与优势。 

![Kotlin Multiplatform](kotlin-multiplatform.svg){width=700}

## Kotlin 多平台使用场景

### Android 与 iOS 应用程序

在移动平台间共享代码是 Kotlin 多平台的主要使用场景之一。 借助 Kotlin 多平台移动端，
可以构建跨平台移动端应用程序并在 Android 与 iOS 之间共享代码， to implement networking,
data storage and data validation, analytics, computations, and other application logic.

查看 [Kotlin 多平台移动端入门](multiplatform-mobile-getting-started.md)与<!--
-->[使用 Ktor 与 SQLDelight 创建多平台应用](multiplatform-mobile-ktor-sqldelight.md)教程，
其中会创建包含两个平台共享代码的模块的 Android 与 iOS 应用程序。

Thanks to [Compose Multiplatform](https://www.jetbrains.com/lp/compose-multiplatform/),
a Kotlin-based declarative UI framework developed by JetBrains,
you can also share UIs across Android and iOS to create fully cross-platform apps:

![Sharing different levels and UI](multiplatform-compose.svg){width=600}

Try this [Compose Multiplatform mobile application](https://github.com/JetBrains/compose-multiplatform-ios-android-template/#readme)
template to create your own mobile application with UIs shared between both platforms.

### 多平台库

Kotlin Multiplatform is also helpful for library authors. You can create a multiplatform library with common code and its
platform-specific implementations for JVM, web, and native platforms. Once published, a multiplatform library can be used
as a dependency in other cross-platform projects.

See the [Create and publish a multiplatform library](multiplatform-library.md) tutorial, where you will create
a multiplatform library, test it, and publish it to Maven.

### Desktop applications

Compose Multiplatform helps share UIs across desktop platforms like Windows, macOS, and Linux. Many applications,
including the [JetBrains Toolbox app](https://blog.jetbrains.com/kotlin/2021/12/compose-multiplatform-toolbox-case-study/),
have already adopted this approach.

Try this [Compose Multiplatform desktop application](https://github.com/JetBrains/compose-multiplatform-desktop-template#readme)
template to create your own project with UIs shared among desktop platforms.

## Code sharing between platforms

Kotlin Multiplatform allows you to maintain a single codebase of the application logic for [different platforms](multiplatform-dsl-reference.md#targets).
You also get advantages of native programming, including great performance and full access to platform SDKs.

Kotlin provides the following code sharing mechanisms:

* Share common code among [all platforms](multiplatform-share-on-platforms.md#share-code-on-all-platforms) used in your project.
* Share code among [some platforms](multiplatform-share-on-platforms.md#share-code-on-similar-platforms) included in your project to reuse much of the code in similar platforms:

  ![Code shared across different platforms](kotlin-multiplatform-hierarchical-structure.svg){width=700}

* 如果需要从共享代码中访问平台相关的 API，请使用 Kotlin 的<!--
-->[预期声明与实际声明](multiplatform-connect-to-apis.md)机制。

## 入门

* Begin with the [Get started with Kotlin Multiplatform for mobile](multiplatform-mobile-getting-started.md) if you want to create iOS and Android applications with shared code
* Explore [sharing code principles and examples](multiplatform-share-on-platforms.md) if you want to create applications or libraries targeting other platforms

> New to Kotlin? Take a look at [Getting started with Kotlin](getting-started.md).
>
{type="tip"}

### 范例项目

浏览跨平台应用程序范例以了解 Kotlin 多平台的工作原理：

* [Kotlin 多平台移动端范例](multiplatform-mobile-samples.md)
* [Compose Multiplatform samples](https://github.com/JetBrains/compose-multiplatform/blob/master/examples/README.md)

