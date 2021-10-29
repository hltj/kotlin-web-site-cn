[//]: # (title: Kotlin/Native 开发动态库——教程)

学校如何使用位于已经存在的<!--
-->原生应用程序或库中的代码。为此，需要将
Kotlin 代码编译为动态库，例如 `.so`、`.dylib` 以及 `.dll`。

Kotlin/Native 也可以与 Apple 技术紧密集成。
这篇 [Kotlin/Native 开发 Apple Framework](apple-framework.md)
教程解释了如何将代码编译为 Swift 或 Objective-C framework。

在这篇教程中，将会：
 - [将 Kotlin 代码编译为动态库](#创建-kotlin-库)
 - [生成 C 的头文件](#生成头文件)
 - [在 C 中调用 Kotlin 动态库](#使用-c-中生成的头文件)
 - 将示例代码编译并运行于 [Linux 与 Mac](#将示例编译并运行于-linux-以及-macos)
   以及 [Windows](#将示例编译并运行于-windows)
  
## 创建 Kotlin 库

Kotlin/Native 编译器可以将 Kotlin 代码编译为一个动态库。
动态库常常带有一个头文件，即 `.h` 文件， 会通过它<!--
-->来调用编译后的 C 代码。

理解这些技术的最佳方法是尝试它们。
首先创建第一个小型的 Kotlin 库，并在 C 程序中使用它。

先在 Kotlin 中创建一个库文件，然后将其保存为 `hello.kt`：

```kotlin
package example

object Object {
  val field = "A"
}

class Clazz {
  fun memberFunction(p: Int): ULong = 42UL
}

fun forIntegers(b: Byte, s: Short, i: UInt, l: Long) { }
fun forFloats(f: Float, d: Double) { }

fun strings(str: String) : String? {
  return "That is '$str' from C"
}

val globalString = "A global String"
```

While it is possible to use the command line, either directly or
by combining it with a script file (such as `.sh` or `.bat` file), this approach doesn't
scale well for big projects that have hundreds of files and libraries.
It is then better to use the Kotlin/Native compiler with a build system, as it
helps to download and cache the Kotlin/Native compiler binaries and libraries with
transitive dependencies and run the compiler and tests.
Kotlin/Native can use the [Gradle](https://gradle.org) build system through the
[kotlin-multiplatform](mpp-discover-project.md#多平台插件) plugin.

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
    binaries {
      sharedLib {
        baseName = "native" // on Linux and macOS
        // baseName = "libnative" // on Windows
      }
    }
  }
}

tasks.wrapper {
  gradleVersion = "%gradleVersion%"
  distributionType = Wrapper.DistributionType.ALL
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
  linuxX64("native") { // on Linux
  // macosX64("native") { // on x86_64 macOS
  // macosArm64("native") { // on Apple Silicon macOS
  // mingwX64("native") { // on Windows
    binaries {
      sharedLib {
        baseName = "native" // on Linux and macOS
        // baseName = "libnative" // on Windows
      }
    }
  }
}

wrapper {
  gradleVersion = "%gradleVersion%"
  distributionType = "ALL"
}
```

</tab>
</tabs>

Move the sources file into the `src/nativeMain/kotlin` folder under
the project. This is the default path, for where sources are located, when
the [kotlin-multiplatform](mpp-discover-project.md#多平台插件)
plugin is used. Use the following block to instruct and configure the project
to generate a dynamic or shared library: 

```kotlin
binaries {
  sharedLib {
    baseName = "native" // on Linux and macOS
    // baseName = "libnative" // on Windows
  }  
}
```

`libnative` 用作库名，即生成的<!--
-->头文件名前缀。它同样也是该头文件中<!--
-->所有声明的前缀。

现在可以<!--
-->[在 IntelliJ IDEA 中打开这个项目](native-get-started.md)<!--
-->并且可以看到如何修正这个示例项目。在我们这样做的时候，
我们将会研究 C 函数如何映射为 Kotlin/Native 声明。

运行这个 `linkNative` Gradle 任务来在 IDE 中构建该库。
或者运行下面这行控制台命令：

```bash
./gradlew linkNative
```

构建将会在 `build/bin/native/debugShared` 文件夹下生成<!--
-->以下文件，并取决于目标操作系统：
- macOS: `libnative_api.h` 与 `libnative.dylib`
- Linux: `libnative_api.h` 与 `libnative.so`
- Windows: `libnative_api.h`、`libnative_symbols.def` 以及 `libnative.dll`

Kotlin/Native 编译器用相似的规则在<!--
-->所有的平台上生成 `.h` 文件。  
来看看我们的 Kotlin 库的 C 语言 API。

## 生成头文件

在 `libnative_api.h` 中，会发现如下代码。
我们来探讨其中部分代码，以便更容易理解。

> Kotlin/Native 的外部符号如有变更，将不会另外说明。

{type="note"}

第一部分包含了标准的 C/C++ 头文件的首尾：

```c
#ifndef KONAN_DEMO_H
#define KONAN_DEMO_H
#ifdef __cplusplus
extern "C" {
#endif

/// 生成的代码的其余部分在这里

#ifdef __cplusplus
}  /* extern "C" */
#endif
#endif  /* KONAN_DEMO_H */
```

在 `libnative_api.h` 中的上述内容之后，会有一个声明通用类型定义的块：

```c
#ifdef __cplusplus
typedef bool            libnative_KBoolean;
#else
typedef _Bool           libnative_KBoolean;
#endif
typedef unsigned short     libnative_KChar;
typedef signed char        libnative_KByte;
typedef short              libnative_KShort;
typedef int                libnative_KInt;
typedef long long          libnative_KLong;
typedef unsigned char      libnative_KUByte;
typedef unsigned short     libnative_KUShort;
typedef unsigned int       libnative_KUInt;
typedef unsigned long long libnative_KULong;
typedef float              libnative_KFloat;
typedef double             libnative_KDouble;
typedef void*              libnative_KNativePtr;
``` 

Kotlin 在已创建的 `libnative_api.h` 文件中为<!--
-->所有的声明都添加了 `libnative_` 前缀。让我们以<!--
-->更容易阅读的方式来查看类型的映射：

|Kotlin 定义。           | C 类型               |
|-----------------------|----------------------|
|`libnative_KBoolean`   | `bool` 或 `_Bool`    |
|`libnative_KChar`      |  `unsigned short`    |
|`libnative_KByte`      |  `signed char`       |
|`libnative_KShort`     |  `short`             |
|`libnative_KInt`       |  `int`               |
|`libnative_KLong`      |  `long long`         |
|`libnative_KUByte`     |  `unsigned char`     |
|`libnative_KUShort`    |  `unsigned short`    |
|`libnative_KUInt`      |  `unsigned int`      |
|`libnative_KULong`     |  `unsigned long long`|
|`libnative_KFloat`     |  `float`             |
|`libnative_KDouble`    |  `double`            |
|`libnative_KNativePtr` |  `void*`             |

这个定义部分展示了如何将 Kotlin 的原始类型映射为 C 的原始类型。
在这篇[从 C 语言中映射原始类型](mapping-primitive-data-types-from-c.md)教程中描述了反向映射。

`libnative_api.h` 文件的下一个部分包含了在该库中<!--
-->使用的类型的定义：

```c
struct libnative_KType;
typedef struct libnative_KType libnative_KType;

typedef struct {
  libnative_KNativePtr pinned;
} libnative_kref_example_Object;

typedef struct {
  libnative_KNativePtr pinned;
} libnative_kref_example_Clazz;
```

`typedef struct { .. } TYPE_NAME` 语法在 C 语言中用于声明一个结构体。
Stackoverflow 上的[这个问题](https://stackoverflow.com/questions/1675351/typedef-struct-vs-struct-definitions)<!--
-->提供了对该模式的更多解释。

As you can see from these definitions, the Kotlin object `Object` is mapped into
`libnative_kref_example_Object`，而 `Clazz` 映射到了 `libnative_kref_example_Clazz`。
这两个结构体都没有包含任何东西，但是 `pinned` 字段是一个指针，该字段类型
`libnative_KNativePtr` 定义在 `void*` 之上。

C 语言中没有支持命名空间，所以 Kotlin/Native 编译器生成了<!--
-->长名称，以避免与现有原生项目中的其他符号发生任何可能的冲突。

定义的很大一部分位于 `libnative_api.h` 文件。
它包含了我们的 Kotlin/Native 库世界的定义：

```c
typedef struct {
  /* Service functions. */
  void (*DisposeStablePointer)(libnative_KNativePtr ptr);
  void (*DisposeString)(const char* string);
  libnative_KBoolean (*IsInstance)(libnative_KNativePtr ref, const libnative_KType* type);

  /* User functions. */
  struct {
    struct {
      struct {
        void (*forIntegers)(libnative_KByte b, libnative_KShort s, libnative_KUInt i, libnative_KLong l);
        void (*forFloats)(libnative_KFloat f, libnative_KDouble d);
        const char* (*strings)(const char* str);
        const char* (*get_globalString)();
        struct {
          libnative_KType* (*_type)(void);
          libnative_kref_example_Object (*_instance)();
          const char* (*get_field)(libnative_kref_example_Object thiz);
        } Object;
        struct {
          libnative_KType* (*_type)(void);
          libnative_kref_example_Clazz (*Clazz)();
          libnative_KULong (*memberFunction)(libnative_kref_example_Clazz thiz, libnative_KInt p);
        } Clazz;
      } example;
    } root;
  } kotlin;
} libnative_ExportedSymbols;
```

这段代码使用了匿名的结构体定义。代码 `struct { .. } foo`
在结构体外部声明了一个<!-- 
-->匿名结构体类型，这个类型没有名字。 

C 语言同样也不支持对象。人们使用函数指针来模仿<!--
-->对象语义。一个函数指针被声明在 `RETURN_TYPE (* FIELD_NAME)(PARAMETERS)` 后面。
它的可读性很差，但我们应该能够从上面的结构体中看到函数指针字段。

### 运行时函数

阅读下面的代码。其中有这个 `libnative_ExportedSymbols` 结构体，它定义了所有
Kotlin/Native 中的以及我们的库提供给我们的函数。它使用<!--
-->嵌套匿名结构，以模仿包。`libnative_` 前缀来源于<!--
-->库的名字。

`libnative_ExportedSymbols` 结构体包含了几个辅助函数：

```c
void (*DisposeStablePointer)(libnative_KNativePtr ptr);
void (*DisposeString)(const char* string);
libnative_KBoolean (*IsInstance)(libnative_KNativePtr ref, const libnative_KType* type);
```

这些函数用于处理 Kotlin/Native 的对象。调用 
`DisposeStablePointer` 来释放一个 Kotlin 对象，而 `DisposeString` 用于释放一个 Kotlin 字符串， 
该字符串具有 C 中的 `char*` 类型。`IsInstance` 函数可以用于检查一个
Kotlin 类型或者一个 `libnative_KNativePtr` 是否是某个类型的实例。实际的<!--
-->生成操作取决于实际的使用情况。
 
Kotlin/Native 拥有垃圾回收机制，但是它不能帮助我们处理<!--
-->来源于 C 的 Kotlin 对象。Kotlin/Native 可以与 Objective-C 以及 
Swift 进行互操作，并且结合了它们的引用计数。 
这篇 [Objective-C 互操作](native-objc-interop.md)<!--
-->包含了更多关于此内容的细节。当然，也可以参考<!--
-->这篇 [Kotlin/Native 开发 Apple Framework](apple-framework.md) 文档。

### 库函数

我们来看看 `kotlin.root.example` 字段，它<!--
-->使用 `kotlin.root.` 前缀模仿 Kotlin 代码的包结构。

这里有一个 `kotlin.root.example.Clazz` 字段用来<!--
-->表示 Kotlin 中的 `Clazz`。这个 `Clazz#memberFunction` 是<!--
-->可以使用 `memberFunction` 字段访问的。唯一的区别是
`memberFunction` 接受 `this` 引用作为第一个参数。
C 语言不支持对象，所以这是为什么明确使用
`this` 指针访问的原因。

`Clazz` 字段中有一个构造函数（又名 `kotlin.root.example.Clazz.Clazz`），
这是创建 `Clazz` 实例的构造函数。

Kotlin `object Object` 是可以被 `kotlin.root.example.Object` 访问的。这里的
`_instance` 函数可以获取到该对象的唯一实例。

属性会转换为函数。`get_` 与 `set_` 前缀<!--
-->分别用于命名 getter 以及 setter 函数。举例来说，
Kotlin 中的只读属性 `globalString` 在 C
中会转换为 `get_globalString` 函数。

全局函数 `forInts`、`forFloats` 以及 `strings`
在 `kotlin.root.example` 匿名结构体中被转换为函数指针。
 
### 入口点

可以看到 API 是如何创建的。首先，需要初始化
`libnative_ExportedSymbols` 结构体。我们来看看
`libnative_api.h` 的最新部分：

```c
extern libnative_ExportedSymbols* libnative_symbols(void);
```

函数 `libnative_symbols` 允许在原生代码中<!--
-->打开 Kotlin/Native 库。This is the entry point you'll use. 该<!--
-->库名称被用作函数名称的前缀。

> Kotlin/Native 对象引用不支持多线程访问。
可能有必要为每个线程托管返回的
`libnative_ExportedSymbols*` 指针。

{type="note"}

## 使用 C 中生成的头文件

使用 C 中的头文件非常简单明了。通过下面的代码创建了一个 `main.c`
文件：

```c
#include "libnative_api.h"
#include "stdio.h"

int main(int argc, char** argv) {
  //获取调用 Kotlin/Native 函数的引用
  libnative_ExportedSymbols* lib = libnative_symbols();

  lib->kotlin.root.example.forIntegers(1, 2, 3, 4);
  lib->kotlin.root.example.forFloats(1.0f, 2.0);

  //使用 C 与 Kotlin/Native 的字符串
  const char* str = "Hello from Native!";
  const char* response = lib->kotlin.root.example.strings(str);
  printf("in: %s\nout:%s\n", str, response);
  lib->DisposeString(response);

  //创建 Kotlin 对象实例
  libnative_kref_example_Clazz newInstance = lib->kotlin.root.example.Clazz.Clazz();
  long x = lib->kotlin.root.example.Clazz.memberFunction(newInstance, 42);
  lib->DisposeStablePointer(newInstance.pinned);

  printf("DemoClazz returned %ld\n", x);

  return 0;
}
```

## 将示例编译并运行于 Linux 以及 macOS

在 macOS 10.13 的 Xcode 上，使用如下命令将 C 代码<!--
-->编译并链接到动态库：

```bash
clang main.c libnative.dylib
```

在 Linux 上使用相似的命令：
```bash
gcc main.c libnative.so
```

编译器生成一个名为 `a.out` 的可执行文件。运行它来查看 Kotlin 代码<!--
-->是如何调用 C 库来运行的。在 Linux 上，将需要将 `.` 引入到 `LD_LIBRARY_PATH`
来使应用程序知晓从当前文件夹加载 `libnative.so` 库。

## 将示例编译并运行于 Windows

首先，需要安装一个支持 64 位目标操作系统的 Microsoft Visual C++
编译器。最简单的方法是在我们的 Windows 机器上安装相同版本的
Microsoft Visual Studio。

在本例中，会使用 `x64 Native Tools Command Prompt <VERSION>` 控制台。会看到<!--
-->在开始菜单中打开控制台的快捷方式。它附带一个 Microsoft Visual Studio
包。

在 Windows 上，动态库可以通过生成的静态库包装器<!--
-->以及手动编写代码的形式导入，使用 [LoadLibrary](https://docs.microsoft.com/en-gb/windows/win32/api/libloaderapi/nf-libloaderapi-loadlibrarya)
处理或类似的 Win32API 功能。使用第一种选项并为
`libnative.dll` 生成静态包装器，如下所述。
 
使用工具链中的 `lib.exe` 来生成静态库<!--
-->包装器 `libnative.lib`，它可以在代码中自动使用 DLL：
```bash
lib /def:libnative_symbols.def /out:libnative.lib
```

现在已经准备好将 `main.c` 编译为可执行文件。将生成的 `libnative.lib`
导入到构建命令并启动：
```bash
cl.exe main.c libnative.lib
```

这行命令生成了 `main.exe` 文件可供执行。

## 接下来

动态库是从现有程序使用 Kotlin 代码的主要方式。
可以使用它们来共享代码到许多平台以及其他语言，包括 JVM、
[Python](https://github.com/JetBrains/kotlin/blob/master/kotlin-native/samples/python_extension/src/main/c/kotlin_bridge.c)、
iOS、Android 以及其他平台。

Kotlin/Native 同样也可以与 Objective-C 以及 Swift 紧密集成。
这部分内容被包含在 [Kotlin/Native 开发 Apple Framework](apple-framework.md)
教程中。
