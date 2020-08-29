---
type: doc
layout: reference
category: "Introduction"
title: "Kotlin/Native"
---

# **Kotlin/Native 用于原生开发**

![Compiler Diagram]({{ url_for('asset', path='images/landing/native/native_overview.png')}})

Kotlin/Native 是一种将 Kotlin 代码编译为无需虚拟机就可运行的原生二进制文件的技术。
它是一个基于 [LLVM](https://llvm.org/) 的 Kotlin 编译器后端以及 Kotlin 标准库的原生实现<!--
-->。

## 为什么选用 Kotlin/Native？

Kotlin/Native 的主要设计目标是让 Kotlin 可以为不希望或者不可能使用 *虚拟机* 的平台<!--
-->（例如嵌入式设备或者 iOS）编译。
它解决了开发人员需要生成<!--
-->无需额外运行时或虚拟机的自包含程序的情况。

## 目标平台

Kotlin/Native 支持以下平台：
   * iOS（arm32、 arm64、 模拟器 x86_64）
   * macOS（x86_64）
   * watchOS (arm32、 arm64、 x86)
   * tvOS (arm64、 x86_64)
   * Android（arm32、arm64、 x86、 x86_64）
   * Windows（mingw x86_64、 x86）
   * Linux（x86_64、 arm32、 arm64、 MIPS、 MIPS 小端次序）
   * WebAssembly（wasm32）

## 互操作

Kotlin/Native 支持与原生世界的双向互操作。
一方面，编译器可创建：
- 用于多个[平台](#目标平台)的可执行文件
- 用于 C/C++ 项目的静态库或[动态](https://www.kotlincn.net/docs/tutorials/native/dynamic-libraries.html)库以及 C 语言头文件
- 用于Swift 与 Objective-C 项目的 [Apple 框架](https://www.kotlincn.net/docs/tutorials/native/apple-framework.html)

另一方面，支持直接在 Kotlin/Native 中使用以下现有库<!--
-->的互操作：
- 静态或动态 [C 语言库](native/c_interop.html)
- C 语言、 [Swift 以及 Objective-C](native/objc_interop.html) 框架

将编译后的 Kotlin 代码包含进<!--
-->用 C、 C++、 Swift、 Objective-C 以及其他语言编写的现有项目中会很容易。
直接在 Kotlin/Native 中使用现有原生代码、
静态或动态 [C 语言库](native/c_interop.html)、
Swift/Objective-C [框架](native/objc_interop.html)、
图形引擎以及任何其他原生内容也很容易。

Kotlin/Native [库](native/platform_libs.html)有助于在多个项目之间共享 Kotlin
代码。
POSIX、 gzip、 OpenGL、 Metal、 Foundation 以及许多其他流行库与
Apple 框架都已预先导入并作为 Kotlin/Native 库包含在编译器包中。

## 在多个平台之间共享代码

不同目标平台的 Kotlin 与 Kotlin/Native 之间支持[多平台项目](multiplatform.html)<!--
-->。
这是在多个平台之间共享公共 Kotlin 代码的方式，这些平台包括 Android、 iOS、 服务器端、 JVM、 客户端、
JavaScript、 CSS 以及原生平台。

[多平台库](multiplatform.html)<!--
-->为公共 Kotlin 代码提供了必要的 API，并有助于在
Kotlin 代码中一次性开发项目的共享部分，从而将其与所有目标平台共享。

## What's next?

Kotlin 新手？可以看看[入门](basic-syntax.html)页。

### Documentation

- [C 语言互操作](native/c_interop.html)
- [Swift/Objective-C 互操作](native/objc_interop.html)

### Tutorials

- [Hello Kotlin/Native](https://www.kotlincn.net/docs/tutorials/native/using-command-line-compiler.html)
- [C 语言 Kotlin/Native 之间的类型映射](https://www.kotlincn.net/docs/tutorials/native/mapping-primitive-data-types-from-c.html)
- [Kotlin/Native 开发动态库](https://www.kotlincn.net/docs/tutorials/native/dynamic-libraries.html)
- [Kotlin/Native 开发 Apple 框架](https://www.kotlincn.net/docs/tutorials/native/apple-framework.html)

## Sample projects

- [Kotlin/Native 源代码与示例](https://github.com/JetBrains/kotlin-native/tree/master/samples)
- [KotlinConf app](https://github.com/JetBrains/kotlinconf-app)
- [KotlinConf Spinner app](https://github.com/jetbrains/kotlinconf-spinner)
- [Kotlin/Native 源代码与示例（.tgz）](https://download.jetbrains.com/kotlin/native/kotlin-native-samples-1.0.1.tar.gz)
- [Kotlin/Native 源代码与示例（.zip）](https://download.jetbrains.com/kotlin/native/kotlin-native-samples-1.0.1.zip)


