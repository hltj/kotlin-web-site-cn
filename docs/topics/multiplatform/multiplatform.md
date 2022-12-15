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

## Kotlin 多平台使用场景

### Android 与 iOS 应用程序

Sharing code between mobile platforms is one of the major Kotlin Multiplatform use cases. With Kotlin Multiplatform Mobile,
you can build cross-platform mobile applications and share common code between Android and iOS, such as business logic, connectivity,
and more.

Check out the [Get started with Kotlin Multiplatform Mobile](multiplatform-mobile-getting-started.md) and
[Create a multiplatform app using Ktor and SQLDelight](multiplatform-mobile-ktor-sqldelight.md) tutorials,
where you will create applications for Android and iOS that include a module with shared code for both platforms.

### 全栈 web 应用程序

Another scenario when code sharing may bring benefits is a connected application where the logic can be
reused on both the server and the client side running in the browser. This is covered by Kotlin
Multiplatform as well.

See [Build a full-stack web app with Kotlin Multiplatform](multiplatform-full-stack-app.md)
tutorial, where you will create a connected application consisting of a server part, using Kotlin/JVM and a web client,
using Kotlin/JS.

### 多平台库

Kotlin Multiplatform is also useful for library authors. You can create a multiplatform library with common code and its
platform-specific implementations for JVM, JS, and Native platforms. Once published, a multiplatform library can be used
in other cross-platform projects as a dependency.

See the [Create and publish a multiplatform library](multiplatform-library.md) tutorial, where you will create
a multiplatform library, test it, and publish it to Maven.

### 移动端与 web 应用程序的公共代码

One more popular case for using Kotlin Multiplatform is sharing the same code across Android, iOS, and web apps. It
reduces the amount of business logic coded by frontend developers and helps implement products more efficiently,
decreasing the coding and testing efforts.

See the [RSS Reader](https://github.com/Kotlin/kmm-production-sample/tree/c6a0d9182802490d17729ae634fb59268f68a447) sample
project — a cross-platform application for iOS and Android with desktop and web clients implemented as experimental features.

## Kotlin 多平台的工作原理

![Kotlin 多平台](kotlin-multiplatform.png){width=500}

* **公共 Kotlin** 包括语言、核心库与基本工具。 用公共 Kotlin 编写的代码适用于<!-- 
-->所有平台的各个地方。
* 借助 Kotlin 多平台库，可以在公共代码以及平台相关代码中复用多平台逻辑。
公共代码可以依赖一组涵盖日常任务的库，例如 [HTTP](https://ktor.io/clients/http-client/multiplatform.html)、 [序列化（serialization）](https://github.com/Kotlin/kotlinx.serialization)与<!--
-->[管理协程（coroutines）](https://github.com/Kotlin/kotlinx.coroutines)。
* 如需与平台互操作，请使用平台相关的 Kotlin 版本。**平台相关的 Kotlin 版本** 
（Kotlin/JVM、 Kotlin/JS、 Kotlin/Native）包含对 Kotlin 语言的扩展以及平台相关的库与工具。 
* 通过这些平台可以访问**平台原生代码**（JVM、 JS 与 Native）并利用所有原生<!--
-->功能。

### 不同平台间共享代码

使用 Kotlin 多平台，花更少的时间为[不同平台](multiplatform-dsl-reference.md#目标)编写并维护相同的代码
——只需使用 Kotlin 提供的机制进行共享即可：

* [在项目中用到的所有平台之间共享代码](multiplatform-share-on-platforms.md#对所有平台共享代码)。 用以共享适用于<!--
-->所有平台的公共业务逻辑。
* 在项目中包含的[某些平台（而不是所有平台）间共享代码](multiplatform-share-on-platforms.md#对相似平台共享代码)。 当<!--
-->可以对类似的平台共享大量代码时请这么用。 

    ![为 iOS 目标共享代码](kotlin-multiplatform-hierarchical-structure.svg){width=700}

* 如果需要从共享代码中访问平台相关的 API，请使用 Kotlin 的<!--
-->[预期声明与实际声明](multiplatform-connect-to-apis.md)机制。

## 入门

* Start with the [Get started with Kotlin Multiplatform Mobile](multiplatform-mobile-getting-started.md) if you want to create iOS and Android applications with shared code
* Look through [sharing code principles and examples](multiplatform-share-on-platforms.md) if you want to create applications or libraries targeting other platforms

> New to Kotlin? Take a look at [Getting started with Kotlin](getting-started.md).
>
{type="tip"}

### 样例项目

浏览跨平台应用程序样例以了解 Kotlin 多平台的工作原理：

* [Kotlin 多平台移动端样例](multiplatform-mobile-samples.md)
* [KotlinConf 应用](https://github.com/JetBrains/kotlinconf-app)
* [KotlinConf Spinner 应用](https://github.com/jetbrains/kotlinconf-spinner)
* [使用 Kotlin 多平台构建全栈 web 应用](multiplatform-full-stack-app.md)
