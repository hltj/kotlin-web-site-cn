[//]: # (title: Kotlin/Native 入门——使用命令行编译器)

## 获取编译器

Kotlin/Native 编译器适用于 macOS、Linux 及 Windows。它是一个命令行工具，作为标准 Kotlin 发行版的<!--
-->一部分提供，可以从 [GitHub 发行版](%kotlinLatestUrl%)下载。它支持包括
iOS （arm32、arm64、simulator x86_64）、Windows （mingw32 及 x86_64）在内的多个不同目标平台，
Linux （x86_64、arm64、MIPS）、macOS （x86_64）、Raspberry PI、STM32、WASM。[关于目标的完整列表，请参见这里](native-overview.md)。
进行跨平台交叉编译，这意味着可以使用一个平台针对另一个平台进行编译，
在这个 %kotlinLatestUrl% 场景中，我们将针对跟本机相同的单平台。

尽管编译器的输出没有任何依赖项或虚拟机要求，
但编译器本身需要 [Java 1.8 或更高版本的运行时](https://jdk.java.net/11/)。

Install the compiler by unpacking its archive to a directory of your choice and adding the path to its `/bin` directory
to the `PATH` environment variable.

## Write "Hello Kotlin/Native" program

该应用程序将在标准输出上打印 "Hello Kotlin/Native"。在选择的工作目录中，创建一个名为
`hello.kt` 的文件，并输入以下内容:

```kotlin
fun main() {
  println("Hello Kotlin/Native!")
}
```

## 在控制台编译代码

要编译这个应用程序，请[下载](https://github.com/JetBrains/kotlin/releases)<!--
-->编译器来执行以下命令:

```bash
kotlinc-native hello.kt -o hello
```

`-o` 选项的值指定了输出文件的名称，所以这个调用应该生成一个 `hello.kexe` （Linux 及 macOS）
或 `hello.exe` （Windows）二进制文件。
关于可用编译器选项的完整列表，请参见[编译器选项参考](compiler-reference.md)。

虽然从控制台编译看起来简单明了，但它<!--
-->对于包含数百个文件和库的大型项目来说，这种方法不太适用。对于现实项目，建议<!--
-->使用[构建系统](native-gradle)和[集成开发环境](native-get-started)。
