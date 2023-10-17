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
可以构建跨平台移动端应用程序并在 Android 与 iOS 之间共享代码， 实现网络、
数据存储与数据的验证、分析、计算以及其他应用程序逻辑。

查看 [Kotlin 多平台移动端入门](multiplatform-mobile-getting-started.md)与<!--
-->[使用 Ktor 与 SQLDelight 创建多平台应用](multiplatform-mobile-ktor-sqldelight.md)教程，
其中会创建包含两个平台共享代码的模块的 Android 与 iOS 应用程序。

借助 [Compose 多平台](https://www.jetbrains.com/lp/compose-multiplatform/)
（JetBrains 开发的基于 Kotlin 的声明式 UI 框架），
还可以跨 Android 与 iOS 共享 UI 来创建完全跨平台的应用：

![共享不同级别与 UI](multiplatform-compose.svg){width=600}

尝试使用这个 [Compose 多平台移动端应用程序](https://github.com/JetBrains/compose-multiplatform-ios-android-template/#readme)<!--
-->模板来创建自己的移动应用程序，并在两个平台间共享 UI。

### 多平台库

Kotlin 多平台对库作者也很有帮助。 可以使用公共代码及其面向
JVM、web 与原生平台的特定于平台的实现来创建多平台库。 发布后，多平台库可以用<!--
-->作其他跨平台项目的依赖项。

参见[创建并发布多平台库](multiplatform-library.md)教程，在该教程中会创建<!--
-->一个多平台库、对其进行测试并将其发布到 Maven。

### 桌面应用程序

Compose Multiplatform helps share UIs across desktop platforms like Windows, macOS, and Linux. Many applications,
including the [JetBrains Toolbox app](https://blog.jetbrains.com/kotlin/2021/12/compose-multiplatform-toolbox-case-study/),
have already adopted this approach.

Try this [Compose Multiplatform desktop application](https://github.com/JetBrains/compose-multiplatform-desktop-template#readme)
template to create your own project with UIs shared among desktop platforms.

## 不同平台间共享代码

Kotlin Multiplatform allows you to maintain a single codebase of the application logic for [different platforms](multiplatform-dsl-reference.md#目标).
You also get advantages of native programming, including great performance and full access to platform SDKs.

Kotlin provides the following code sharing mechanisms:

* Share common code among [all platforms](multiplatform-share-on-platforms.md#对所有平台共享代码) used in your project.
* Share code among [some platforms](multiplatform-share-on-platforms.md#对相似平台共享代码) included in your project to reuse much of the code in similar platforms:

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
* [Compose 多平台范例](https://github.com/JetBrains/compose-multiplatform/blob/master/examples/README.md)

