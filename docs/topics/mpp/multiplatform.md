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

With Kotlin Multiplatform, spend less time on writing and maintaining the same code for [different platforms](mpp-supported-platforms.md)
 – just share it using the mechanisms Kotlin provides:

* [Share code among all platforms used in your project](mpp-share-on-platforms.md#对所有平台共享代码). Use it for sharing the common 
business logic that applies to all platforms. 
     
    ![Code shared for all platforms](flat-structure.png)
    
* [Share code among some platforms](mpp-share-on-platforms.md#对相似平台共享代码) included in your project but not all. Do this 
when you can reuse much of the code in similar platforms.  
    
    ![Hierarchical structure](hierarchical-structure.png)

    ![Code shared for iOS targets](iosmain-hierarchy.png){width=400}

If you need to access platform-specific APIs from the shared code, use the Kotlin mechanism of [expected and actual 
declarations](mpp-connect-to-apis.md).

With this mechanism, a common source set defines an _expected declaration_, and platform source sets must provide the 
_actual declaration_ that corresponds to the expected declaration. This works for most Kotlin declarations, such as 
functions, classes, interfaces, enumerations, properties, and annotations.

![Expect and actual declarations](expect-actual.png){width=700}

```kotlin
//Common
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

移动平台之间共享代码是 Kotlin 多平台的主要使用场景之一。 With Kotlin Multiplatform Mobile (KMM),
you can build multiplatform mobile applications sharing code, such as business logic, connectivity, 
and more, between Android and iOS.

See [KMM features, case studies and examples](https://kotlinlang.org/lp/mobile/)

### 客户端——服务器端

代码共享可以带来收益的另一个场景是互联应用，其中的逻辑可以<!--
-->在服务器与运行在浏览器中的客户端中复用。Kotlin 多平台也覆盖了<!--
-->这个场景。

[Ktor 框架](https://ktor.kotlincn.net/)适用于在互联系统中构建异步的服务器与客户端。

## 接下来做什么？

New to Kotlin? Visit [Getting started with Kotlin](getting-started.md).

### 文档

* [Get started with Kotlin Multiplatform Mobile (KMM)](kmm-getting-started.md)
* [Create a multiplatform project](mpp-create-lib.md)
* [Share code on multiple platforms](mpp-share-on-platforms.md)
* [Connect to platform-specific APIs](mpp-connect-to-apis.md)

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

- [Kotlin Multiplatform Mobile (KMM) samples](kmm-samples.md)
- [KotlinConf app](https://github.com/JetBrains/kotlinconf-app) 
- [KotlinConf Spinner app](https://github.com/jetbrains/kotlinconf-spinner)
