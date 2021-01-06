---
type: doc
layout: reference
title: "Kotlin 多平台"
---

# Kotlin 多平台

> 多平台项目处于 [Alpha](evolution/components-stability.html) 版。语言特性与工具都可能在未来的 Kotlin 版本中发生变化。
{:.note}

支持多平台编程是 Kotlin 的主要优势之一。
这减少了为[不同平台](mpp-supported-platforms.html)编写与维护相同代码所花费的时间，同时保留了原生编程的灵活性和优势。
了解有关 [Kotlin 多平台的好处](multiplatform.html)的更多信息。

通过 Kotlin 多平台，使用 Kotlin 提供的机制共享代码：
 
*   [在项目中使用的所有平台之间共享代码](mpp-share-on-platforms.html#对所有平台共享代码)。
    使用它共享适用于所有平台的通用业务逻辑。
     
    ![所有平台共享的代码]({{ url_for('asset', path='images/reference/mpp/flat-structure.png') }})
    
*   在项目中包含的[某些平台之间共享代码](mpp-share-on-platforms.html#对相似平台共享代码)，但不是全部。
    可以使用分层结构在相似平台中重用许多代码。
    可以将[目标快捷方式](mpp-share-on-platforms.html#使用目标快捷方式)用于目标的常见组合，也可以[手动配置层次结构](mpp-share-on-platforms.html#手动配置层次结构)。
    
    <img class="img-responsive" src="{{ url_for('asset', path='images/reference/mpp/iosmain-hierarchy.png') }}" alt="为 iOS 目标共享的代码" width="400"/>

    ![层次结构]({{ url_for('asset', path='images/reference/mpp/hierarchical-structure.png') }})

如果需要从共享代码访问特定于平台的 API，
可以使用[预期的与实际声明的](mpp-connect-to-apis.html) Kotlin 机制。

## 教程

* [创建多平台的 Kotlin 库](/docs/tutorials/mpp/multiplatform-library.html)
  教授如何创建可用于 JVM、JS 与原生的多平台库，
  并且可以从任何其他常见代码（例如，Android 与 iOS 共享）中使用该库。
  还展示了如何编写将在所有平台上执行的测试以及如何使用特定平台提供的有效实现。
 
* [使用 Kotlin 多平台构建全栈 Web 应用](https://play.kotlinlang.org/hands-on/Full%20Stack%20Web%20App%20with%20Kotlin%20Multiplatform/01_Introduction) 
  通过构建使用共享代码，序列化与其他多平台范式的客户端服务器应用程序，
  讲授了构建针对 Kotlin/JVM 与 Kotlin/JS 的应用程序的概念。
  它还简要介绍了如何将 Ktor 作为服务器与客户端框架使用。
