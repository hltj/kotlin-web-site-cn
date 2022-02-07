[//]: # (title: Kotlin Multiplatform)
[//]: # (description: Kotlin Multiplatform allows creating cross-platform apps for desktop, web, and mobile devices.
Share application logic while keeping native user experience.)

> 多平台项目处于 [Alpha](components-stability.md)版。语言特性与工具都可能在未来的 Kotlin 版本中发生变化。
>
{type="note"}

支持多平台程序设计是 Kotlin 的主要优势之一。 它减少了为[不同平台](mpp-supported-platforms.md)<!--
-->编写与维护相同代码所花的时间，同时保留了原生编程的灵活性与优势。 

## Kotlin Multiplatform use cases

### Android and iOS applications

Sharing code between mobile platforms is one of the major Kotlin Multiplatform use cases. With Kotlin Multiplatform Mobile,
you can build cross-platform mobile applications and share common code between Android and iOS, such as business logic, connectivity,
and more.

Check out the [Get started with Kotlin Multiplatform Mobile](https://kotlinlang.org/lp/mobile/) section and
[Kotlin Multiplatform Hands-on: Networking and Data Storage](https://play.kotlinlang.org/hands-on/Networking%20and%20Data%20Storage%20with%20Kotlin%20Multiplatfrom%20Mobile/01_Introduction),
where you will create an application for Android and iOS that includes a module with shared code for both platforms.

### Full-stack web applications

Another scenario when code sharing may bring benefits is a connected application where the logic can be
reused on both the server and the client side running in the browser. This is covered by Kotlin
Multiplatform as well.

See [Build a Full Stack Web App with Kotlin Multiplatform](https://play.kotlinlang.org/hands-on/Full%20Stack%20Web%20App%20with%20Kotlin%20Multiplatform/01_Introduction)
hands-on, where you will create a connected application consisting of a server part, using Kotlin/JVM and a web client,
using Kotlin/JS.

### Multiplatform libraries

Kotlin Multiplatform is also useful for library authors. You can create a multiplatform library with common code and its
platform-specific implementations for JVM, JS, and Native platforms. Once published, a multiplatform library can be used
in other cross-platform projects as a dependency.

See the [Create and publish a multiplatform library](multiplatform-library.md) tutorial, where you will create
a multiplatform library, test it, and publish it to Maven.

### Common code for mobile and web applications

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

### Code sharing between platforms

使用 Kotlin 多平台，花更少的时间为[不同平台](mpp-supported-platforms.md)编写并维护相同的代码
——只需使用 Kotlin 提供的机制进行共享即可：

* [在项目中用到的所有平台之间共享代码](mpp-share-on-platforms.md#对所有平台共享代码)。 用以共享适用于<!--
-->所有平台的公共业务逻辑。

* 在项目中包含的[某些平台（而不是所有平台）间共享代码](mpp-share-on-platforms.md#对相似平台共享代码)。 当<!--
-->可以对类似的平台共享大量代码时请这么用。 

    ![为 iOS 目标共享代码](kotlin-multiplatofrm-hierarchical-structure.png){width=700}

* 如果需要从共享代码中访问平台相关的 API，请使用 Kotlin 的<!--
-->[预期声明与实际声明](mpp-connect-to-apis.md)机制。

使用这一机制，公共源代码集定义一个*预期（expect）声明*，平台源代码集必须提供<!--
-->与该预期声明对应的*实际（actual）声明*。 这适用于大多数 Kotlin 声明，例如<!-- 
-->函数、 类、 接口、 枚举、 属性以及注解。

## Get started

* Look through [our examples and tutorials](mpp-share-on-platforms.md) if you want to create applications or libraries targeting JVM, JavaScript, and other platforms
* Start with the [Get started with Kotlin Multiplatform Mobile](kmm-getting-started.md) if you want to create iOS and Android applications with shared code

> New to Kotlin? Take a look at [Getting started with Kotlin](getting-started.md).
>
{type="tip"}

### 样例项目

Look through cross-platform application samples to understand how Kotlin Multiplatform works:

* [Kotlin Multiplatform Mobile samples](kmm-samples.md)
* [KotlinConf 应用](https://github.com/JetBrains/kotlinconf-app)
* [KotlinConf Spinner 应用](https://github.com/jetbrains/kotlinconf-spinner)
* [Build a Full Stack Web App with Kotlin Multiplatform hands-on](https://play.kotlinlang.org/hands-on/Full%20Stack%20Web%20App%20with%20Kotlin%20Multiplatform/01_Introduction)
