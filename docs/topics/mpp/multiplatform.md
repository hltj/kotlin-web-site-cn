[//]: # (title: 多平台程序设计)

> 多平台项目处于 [Alpha](components-stability.md)版。语言特性与工具都可能在未来的 Kotlin 版本中发生变化。
>
{type="note"}

支持多平台程序设计是 Kotlin 的主要优势之一。 它减少了为[不同平台](mpp-supported-platforms.md)<!--
-->编写与维护相同代码所花的时间，同时保留了原生编程的灵活性与优势。 

以下是 Kotlin 多平台的工作原理。

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

使用 Kotlin 多平台，花更少的时间为[不同平台](mpp-supported-platforms.md)编写并维护相同的代码
——只需使用 Kotlin 提供的机制进行共享即可：

* [在项目中用到的所有平台之间共享代码](mpp-share-on-platforms.md#对所有平台共享代码)。 用以共享适用于<!--
-->所有平台的公共业务逻辑。
     
    ![所有平台共享的代码](flat-structure.png)
    
* 在项目中包含的[某些平台（而不是所有平台）间共享代码](mpp-share-on-platforms.md#对相似平台共享代码)。 当<!--
-->可以对类似的平台共享大量代码时请这么用。 
    
    ![分层结构](hierarchical-structure.png)

    ![为 iOS 目标共享代码](iosmain-hierarchy.png){width=400}

如果需要从共享代码中访问平台相关的 API，请使用 Kotlin 的<!--
-->[预期声明与实际声明](mpp-connect-to-apis.md)机制。

使用这一机制，公共源代码集定义一个*预期（expect）声明*，平台源代码集必须提供<!--
-->与该预期声明对应的*实际（actual）声明*。 这适用于大多数 Kotlin 声明，例如<!-- 
-->函数、 类、 接口、 枚举、 属性以及注解。

![Expect and actual declarations](expect-actual.png){width=700}

```kotlin
//公共
expect fun randomUUID(): String
```

```kotlin
//Android
import java.util.*
actual fun randomUUID() = UUID.randomUUID().toString()
```

```kotlin
//iOS
import platform.Foundation.NSUUID
actual fun randomUUID(): String = NSUUID().UUIDString()
```

## 使用场景

### Android——iOS

移动平台之间共享代码是 Kotlin 多平台的主要使用场景之一。 借助 Kotlin 多平台移动端（KMM，Kotlin Multiplatform Mobile），
可以构建在 Android 与 iOS 之间共享代码的多平台移动端应用程序，
例如可共享业务逻辑、连接性等。

参见 [KMM 特性、案例研究与示例](https://kotlinlang.org/lp/mobile/)

### 客户端——服务器端

代码共享可以带来收益的另一个场景是互联应用，其中的逻辑可以<!--
-->在服务器与运行在浏览器中的客户端中复用。Kotlin 多平台也覆盖了<!--
-->这个场景。

[Ktor 框架](https://ktor.kotlincn.net/)适用于在互联系统中构建异步的服务器与客户端。

## 接下来做什么？

刚接触 Kotlin？可以看看 [Kotlin 入门](getting-started.md)页。

### 文档

* [Kotlin 多平台移动端（KMM）入门](kmm-getting-started.md)
* [创建多平台项目](mpp-create-lib.md)
* [在多个平台上共享代码](mpp-share-on-platforms.md)
* [接入平台相关 API](mpp-connect-to-apis.md)

### 教程

* [Create your first KMM application](kmm-create-first-app.md) shows how to create a mobile application that works on Android and iOS with the help of the [KMM plugin for Android Studio](https://plugins.jetbrains.com/plugin/14936-kotlin-multiplatform-mobile).
Create, run, and test your first multiplatform mobile application.

* [Creating a multiplatform Kotlin library](multiplatform-library.md) teaches how to create a multiplatform 
library available for JVM, JS, and Native and which can be used from any other common code (for example, shared with 
Android and iOS). It also shows how to write tests which will be executed on all platforms and use an efficient implementation
 provided by a specific platform.
 
* [Building a full stack web app with Kotlin Multiplatform](https://play.kotlinlang.org/hands-on/Full%20Stack%20Web%20App%20with%20Kotlin%20Multiplatform/01_Introduction) 
  teaches the concepts behind building an application that targets Kotlin/JVM and Kotlin/JS by building a client-server 
  application that makes use of shared code, serialization, and other multiplatform paradigms. It also provides a brief
  introduction to working with Ktor both as a server- and client-side framework.
  
## 样例项目

- [Kotlin 多平台移动端（KMM）样例](kmm-samples.md)
- [KotlinConf app](https://github.com/JetBrains/kotlinconf-app) 
- [KotlinConf Spinner app](https://github.com/jetbrains/kotlinconf-spinner)
