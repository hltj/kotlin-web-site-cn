[//]: # (title: 映射来自 C 语言的原生数据类型——教程)

在本教程中会学习到如何将 C 的数据类型在 Kotlin/Native 中可见，反之亦然。将会：
- 看到什么是 [C 语言中的数据类型](#c-语言中的类型)
- 创建一个[小型 C 库](#c-库示例)来使这些类型向外暴露
- [探查为 C 库生成的 Kotlin API](#探查为-c-库生成的-kotlin-api)
- 找到如何将 [Kotlin 中的原生类型](#kotlin-中的原生类型)映射到 C 的方法

## C 语言中的类型

在 C 语言中都有什么类型？我们以维基百科上的
[C 语言数据类型](https://en.wikipedia.org/wiki/C_data_types)这篇文章作为基础。
在 C 语言中有如下这些类型：
- 基本类型 `char、int、float、double` 以及带修饰符的 `signed、unsigned、short、long`
- 结构体、联合体、数组
- 指针
- 函数指针

还有一些更多的具体类型：
- 布尔类型（源于 [C99](https://en.wikipedia.org/wiki/C99)）
- `size_t` 与 `ptrdiff_t`（也作 `ssize_t`）
- 固定长度的整型例如：`int32_t` 或 `uint64_t`（源于 [C99](https://en.wikipedia.org/wiki/C99)）

C 语言中还有以下类型限定符：`const`、`volatile`、`restruct`、`atomic`。

在 Kotlin 中查看 C 数据类型的最佳方法就是尝试一下。

## C 库示例

创建一个 `lib.h` 文件来看看如何将 C 函数映射到 Kotlin：

```c
#ifndef LIB2_H_INCLUDED
#define LIB2_H_INCLUDED

void ints(char c, short d, int e, long f);
void uints(unsigned char c, unsigned short d, unsigned int e, unsigned long f);
void doubles(float a, double b);

#endif
```

该文件缺少了本例不需要的 `extern "C"` 块，但是如果<!--
-->在使用 C++ 的重载函数的时候这也许是必要的。
[C++ 兼容性问题](https://stackoverflow.com/questions/1041866/what-is-the-effect-of-extern-c-in-c)<!--
-->包含了更多关于此内容的细节。

对于每组 `.h` 文件，
使用来自 Kotlin/Native 的 [`cinterop` 工具](native-c-interop.md)<!--
-->来生成 Kotlin/Native 库，
或者 `.klib`。生成的库将会桥接 Kotlin/Native 到 C 语言的调用。这包括<!--
-->在 `.h` 文件中各定义的 Kotlin 声明。
只需要一个 `.h` 文件来运行 `cinterop` 工具。并且不需要创建一个
`lib.c` 文件，除非想编译并运行该示例。
更多关于这些内容的细节被涵盖在[与 C 语言互操作](native-c-interop.md)页面。这篇教程<!--
-->的内容足够使用下面的内容来创建 `lib.def` 文件：

```c
headers = lib.h
```

可以在 `---` 分隔符之后将所有声明直接包含在 `.def` 文件中。
将宏或其他 C 定义包含在 `cinterop` 工具生成的代码中会很有帮助。
方法体同样被编译以及完全包含到二进制文件中。使用<!--
-->这个功能并且在不使用 C 编译器的情况下来得到一个可运行的示例。
为了实现这个，需要在 `lib.h` 文件中添加 C 函数的实现，
并将这些函数放入 `.def` 文件中。
会得到如下的`interop.def` 结果：

```c

---

void ints(char c, short d, int e, long f) { }
void uints(unsigned char c, unsigned short d, unsigned int e, unsigned long f) { }
void doubles(float a, double b) { }
```

`interop.def` 文件足以编译和运行应用程序或在 IDE 中打开它。
现在是时候创建项目文件，且在
[IntelliJ IDEA](https://jetbrains.com/idea) 中打开并运行它。 

## 探查为 C 库生成的 Kotlin API

While it is possible to use the command line, either directly or
by combining it with a script file (such as `.sh` or `.bat` file), this approach doesn't
scale well for big projects that have hundreds of files and libraries.
It is then better to use the Kotlin/Native compiler with a build system, as it
helps to download and cache the Kotlin/Native compiler binaries and libraries with
transitive dependencies and run the compiler and tests.
Kotlin/Native can use the [Gradle](https://gradle.org) build system through the [kotlin-multiplatform](mpp-discover-project.md#多平台插件) plugin.

We covered the basics of setting up an IDE compatible project with Gradle in the
[A Basic Kotlin/Native Application](native-gradle.md)
tutorial. Please check it out if you are looking for detailed first steps
and instructions on how to start a new Kotlin/Native project and open it in IntelliJ IDEA.
In this tutorial, we'll look at the advanced C interop related usages of Kotlin/Native and [multiplatform](mpp-discover-project.md#多平台插件) builds with Gradle.

First, create a project folder. All the paths in this tutorial will be relative to this folder. Sometimes
the missing directories will have to be created before any new files can be added.

Use the following `build.gradle(.kts)` Gradle build file:

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
plugins {
    kotlin("multiplatform") version "%kotlinVersion%"
}

repositories {
    mavenCentral()
}

kotlin {
  linuxX64("native") { // on Linux
  // macosX64("native") { // on x86_64 macOS
  // macosArm64("native") { // on Apple Silicon macOS
  // mingwX64("native") { // on Windows
    val main by compilations.getting
    val interop by main.cinterops.creating
    
    binaries {
      executable()
    }
  }
}

tasks.wrapper {
  gradleVersion = "%gradleVersion%"
  distributionType = Wrapper.DistributionType.BIN
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
plugins {
    id 'org.jetbrains.kotlin.multiplatform' version '%kotlinVersion%'
}

repositories {
    mavenCentral()
}

kotlin {
  linuxX64('native') { // on Linux
  // macosX64("native") { // on x86_64 macOS
  // macosArm64("native") { // on Apple Silicon macOS
  // mingwX64('native') { // on Windows
    compilations.main.cinterops {
      interop 
    }
    
    binaries {
      executable()
    }
  }
}

wrapper {
  gradleVersion = '%gradleVersion%'
  distributionType = 'BIN'
}
```

</tab>
</tabs>

The project file configures the C interop as an additional step of the build.
Let's move the `interop.def` file to the `src/nativeInterop/cinterop` directory.
Gradle recommends using conventions instead of configurations,
for example, the source files are expected to be in the `src/nativeMain/kotlin` folder.
By default, all the symbols from C are imported to the `interop` package,
you may want to import the whole package in our `.kt` files.
Check out the [kotlin-multiplatform](mpp-discover-project.md#多平台插件)
plugin documentation to learn about all the different ways you could configure it.

使用以下内容来创建一个 `src/nativeMain/kotlin/hello.kt` 存根文件<!--
-->来查看 C 的原生类型声明是如何在 Kotlin 中可见的：

```kotlin
import interop.*

fun main() {
  println("Hello Kotlin/Native!")
  
  ints(/* fix me*/)
  uints(/* fix me*/)
  doubles(/* fix me*/)
}
```

现在已经准备好<!--
-->[在 IntelliJ IDEA 中打开这个项目](native-get-started.md)<!--
-->并且看看如何修正这个示例项目。当做完这些之后，
检查 C 的原生类型是如何映射到 Kotlin/Native 的。

## Kotlin 中的原生类型

在 IntelliJ IDEA 的 __Go to | Declaration__
或编译器错误的帮助下会看到为 C 函数生成的 API：

```kotlin
fun ints(c: Byte, d: Short, e: Int, f: Long)
fun uints(c: UByte, d: UShort, e: UInt, f: ULong)
fun doubles(a: Float, b: Double)
```

C 类型按照我们期望的方式进行了映射，注意，`char` 类型映射到了 `kotlin.Byte` ，
因为它通常是 8 位有符号值。

| C | Kotlin |
|---|--------|
| char  |  kotlin.Byte |
| unsigned char  |  kotlin.UByte |
| short |  kotlin.Short |
| unsigned short |  kotlin.UShort |
| int   |  kotlin.Int |
| unsigned int   |  kotlin.UInt |
| long long  |  kotlin.Long |
| unsigned long long |  kotlin.ULong |
| float |  kotlin.Float |
| double | kotlin.Double |

## 修正代码

看过了所有的定义并且是时候来修改代码了。
[在 IDE 中](native-get-started.md)运行 `runDebugExecutableNative` Gradle 任务<!--
-->或使用下面的命令来运行代码：

```bash
./gradlew runDebugExecutableNative
```

`hello.kt` 文件中的代码最终看起来会是这样的：
 
```kotlin
import interop.*

fun main() {
  println("Hello Kotlin/Native!")
  
  ints(1, 2, 3, 4)
  uints(5, 6, 7, 8)
  doubles(9.0f, 10.0)
}
```

## 接下来

在接下来的几篇教程中继续探索更复杂的
C 语言类型及其在 Kotlin/Native 中的表示：
- [映射来自 C 语言的结构与联合类型](mapping-struct-union-types-from-c.md)
- [映射来自 C 语言的函数指针](mapping-function-pointers-from-c.md)
- [映射来自 C 语言的字符串](mapping-strings-from-c.md)

这篇[与 C 语言互操作文档](native-c-interop.md)<!--
-->涵盖了更多的高级互操作场景。
