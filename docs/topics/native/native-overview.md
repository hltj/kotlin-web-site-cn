[//]: # (title: Kotlin/Native)

Kotlin/Native 是一种将 Kotlin 代码编译为无需虚拟机就可运行的原生二进制文件的技术。
Kotlin/Native 包含一个基于 [LLVM](https://llvm.org/) 的 Kotlin 编译器后端以及Kotlin 标准库的<!--
-->原生实现。

## 为什么选用 Kotlin/Native？

Kotlin/Native 的主要设计目标是让 Kotlin 可以为不希望或者不可能使用*虚拟机*的平台<!--
-->（例如嵌入式设备或者 iOS）编译。它非常适合当你需要生成<!--
-->无需额外运行时或虚拟机的自包含程序的情况。

It's easy to include compiled Kotlin code in existing projects written in C, C++, Swift, Objective-C, and other languages.
You can also use existing native code, static or dynamic C libraries, Swift/Objective-C frameworks, graphical engines,
and anything else directly from Kotlin/Native.

<a href="native-get-started.md"><img src="native-get-started-button.svg" width="350" alt="Get started with Kotlin/Native" style="block"/></a>

## 目标平台

Kotlin/Native 支持以下平台：

* Linux
* Windows（通过 [MinGW](https://www.mingw-w64.org/)）
* [Android NDK](https://developer.android.com/ndk)
* Apple 目标平台，面向 macOS、 iOS、 tvOS 与 watchOS

  > 如需编译苹果目标，需要安装 [Xcode](https://apps.apple.com/us/app/xcode/id497799835)
  > 及其命令行工具。
  >
  {style="note"}

[参见所支持目标的完整列表](native-target-support.md)。

## 互操作

Kotlin/Native 支持与不同操作系统的原生编程语言的双向互操作。
The compiler can create executables for many platforms, static or dynamic C libraries, and Swift/Objective-C frameworks.

### 与 C 语言互操作

Kotlin/Native provides [interoperability with C](native-c-interop.md). You can use existing C libraries directly from
Kotlin code.

To learn more, complete the following tutorials:

* [Create a dynamic library with C headers for C/C++ projects](native-dynamic-libraries.md)
* [Learn how C types are mapped into Kotlin](mapping-primitive-data-types-from-c.md)
* [Create a native HTTP client using C interop and libcurl](native-app-with-c-and-libcurl.md)

### 与 Swift/Objective-C 互操作

Kotlin/Native provides [interoperability with Swift through Objective-C](native-objc-interop.md). You can use
Kotlin code directly from Swift/Objective-C applications on macOS and iOS.

To learn more, complete the [Kotlin/Native as an Apple framework](apple-framework.md) tutorial.

## 在多个平台之间共享代码

Kotlin/Native includes a set of prebuilt [platform libraries](native-platform-libs.md) that help share Kotlin code
between projects. POSIX, gzip, OpenGL, Metal, Foundation, and many other popular libraries and Apple frameworks
are pre-imported and included as Kotlin/Native libraries in the compiler package.

Kotlin/Native is a part of the [Kotlin Multiplatform](https://www.jetbrains.com/help/kotlin-multiplatform-dev/get-started.html) technology that helps share common code
across multiple platforms, including Android, iOS, JVM, web, and native. Multiplatform libraries provide the necessary
APIs for common Kotlin code and allow writing shared parts of projects in Kotlin all in one place.

## 内存管理器

Kotlin/Native uses an automatic [memory manager](native-memory-manager.md) that is similar to the JVM and Go.
It has its own tracing garbage collector, which is also integrated with Swift/Objective-C's ARC.

The memory consumption is controlled by a custom memory allocator. It optimizes memory usage and helps prevent sudden
surges in memory allocation.