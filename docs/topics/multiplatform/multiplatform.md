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

Compose 多平台有助于跨桌面平台（如 Windows、macOS 与 Linux）共享 UI。 包括
[JetBrains Toolbox 应用](https://blog.jetbrains.com/kotlin/2021/12/compose-multiplatform-toolbox-case-study/)在内的许多应用程序<!--
-->都已经采用了这种方式。

尝试使用这个 [Compose 多平台桌面应用程序](https://github.com/JetBrains/compose-multiplatform-desktop-template#readme)<!--
-->模板来创建自己的项目，并在桌面平台间共享 UI。

## 不同平台间共享代码

Kotlin 多平台让你能为[不同平台](multiplatform-dsl-reference.md#目标)的应用程序逻辑维护一份代码。
还可以获得原生程序设计的优势，包括出色的性能以及对平台 SDK 的完全访问能力。

Kotlin 提供了以下代码共享机制：

* 在项目中使用的[所有平台](multiplatform-share-on-platforms.md#对所有平台共享代码)之间共享公共代码。
* 在项目中包含的[一些平台](multiplatform-share-on-platforms.md#对相似平台共享代码)之间共享代码，以便在类似平台中复用大量代码：

  ![在不同平台间共享代码](kotlin-multiplatform-hierarchical-structure.svg){width=700}

* 如果需要从共享代码中访问平台相关的 API，请使用 Kotlin 的<!--
-->[预期声明与实际声明](multiplatform-connect-to-apis.md)机制。

## 入门

* 如果想使用共享代码创建 iOS 与 Android 应用程序，请从[用于移动端的 Kotlin 多平台入门](multiplatform-mobile-getting-started.md)开始
* 如果想创建面向其他平台的应用程序或库，请研读[共享代码原则与示例](multiplatform-share-on-platforms.md)

> 刚接触 Kotlin？可以看看 [Kotlin 入门](getting-started.md)。
>
{type="tip"}

### 范例项目

浏览跨平台应用程序范例以了解 Kotlin 多平台的工作原理：

* [Kotlin 多平台移动端范例](multiplatform-mobile-samples.md)
* [Compose 多平台范例](https://github.com/JetBrains/compose-multiplatform/blob/master/examples/README.md)

