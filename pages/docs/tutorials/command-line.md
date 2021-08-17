---
type: tutorial
layout: tutorial
title: "使用命令行编译器"
description: "本教程将引导我们使用命令行编译器创建 Hello World 应用程序。"
authors: Hadi Hariri，johnpoint、高金龙（翻译）
showAuthorInfo: true
related:
    - getting-started.md
---
### 下载编译器

每个版本都附带一个独立版本的编译器。可以从 [GitHub Releases]({{ site.data.releases.latest.url }}) 页下载最新版本（`kotlin-compiler-{{ site.data.releases.latest.version }}.zip`）。

#### 手动安装
将独立的编译器解压缩到一个目录中，并将 `bin` 目录添加到系统路径中。`bin` 目录包含了在 Windows、OS X 及 Linux 上编译与运行 Kotlin 所需的脚本。

#### SDKMAN!
在基于 UNIX 的系统（如 OS X、Linux、Cygwin、FreeBSD 和 Solaris）上安装 Kotlin 的更简单方法是使用 [SDKMAN!](http://sdkman.io)。
只需在终端中运行以下命令并按照说明操作即可：

<div class="sample" markdown="1" mode="shell" theme="idea">

```bash
$ curl -s https://get.sdkman.io | bash
```

</div>

接下来打开一个新终端并安装 Kotlin：

<div class="sample" markdown="1" mode="shell" theme="idea">

```bash
$ sdk install kotlin
```

</div>

#### Homebrew
或者，在 OS X上，可以通过 [Homebrew](http://brew.sh/) 安装编译器。

<div class="sample" markdown="1" mode="shell" theme="idea">

```bash
$ brew update
$ brew install kotlin
```

</div>

#### [Snap](https://snapcraft.io/) 包
如果使用的是 Ubuntu 16.04 或更高版本，那么可以在命令行安装编译器：

<div class="sample" markdown="1" mode="shell" theme="idea">

```bash
$ sudo snap install --classic kotlin
```

</div>

### 创建并运行第一个应用程序

1. 在 Kotlin 中创建一个显示 `"Hello, World!"` 的简单应用程序。使用喜欢的编辑器，创建一个名为 *hello.kt* 的新文件，内容如下：

   <div class="sample" markdown="1" theme="idea">

   ```kotlin
   fun main() {
       println("Hello, World!")
   }
   ```

   </div>

2. 使用 Kotlin 编译器编译应用程序：

    <div class="sample" markdown="1" mode="shell" theme="idea">

    ```bash
    $ kotlinc hello.kt -include-runtime -d hello.jar
    ```

    </div>

   `-d` 选项表示生成的类文件的输出路径，可以是目录，也可以是一个 *.jar* 文件。`-include-runtime` 选项通过在其中包含 Kotlin 运行时库，使得生成的 *.jar* 文件中自包含运行时库且可运行。
   如果要查看所有可用选项，请运行以下命令：

    <div class="sample" markdown="1" mode="shell" theme="idea">

    ```bash
    $ kotlinc -help
    ```

    </div>

3. 运行该应用程序。

    <div class="sample" markdown="1" mode="shell" theme="idea">

    ```bash
    $ java -jar hello.jar
    ```

    </div>


### 编译库

如果你正在开发一个供其他 Kotlin 应用程序使用的库，那么可以构建 *.jar* 文件，而不将 Kotlin 运行时包含在其中。

<div class="sample" markdown="1" mode="shell" theme="idea">

```bash
$ kotlinc hello.kt -d hello.jar
```

</div>

   由于以这种方式编译的二进制文件依赖于 Kotlin 运行时，因此无论何时使用编译库，都应确保后者存在于类路径中。

   还可以使用 `kotlin` 脚本来运行 Kotlin 编译器生成的二进制文件：

<div class="sample" markdown="1" mode="shell" theme="idea">

```bash
$ kotlin -classpath hello.jar HelloKt
```

</div>

   `HelloKt` 是 Kotlin 编译器为名为 `hello.kt` 的文件生成的主类名。

### 运行 REPL

可以运行没有参数的编译器来拥有交互式 shell。可以输入任何有效的 Kotlin 代码并查看结果。

![Shell]({{ url_for('tutorial_img', filename='command-line/kotlin_shell.png')}})

### 使用命令行运行脚本

Kotlin 也可以用作脚本语言。脚本是具有顶层可执行代码的 Kotlin 源文件（*.kts*）。

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
import java.io.File

// 获取传入的路径，即“-d 一些/路径”或使用当前路径。
val path = if (args.contains("-d")) args[1 + args.indexOf("-d")]
           else "."

val folders = File(path).listFiles { file -> file.isDirectory() }
folders?.forEach { folder -> println(folder) }
```

</div>

要运行脚本，我们只需使用相应的脚本文件将 `-script` 选项传递给编译器。

<div class="sample" markdown="1" mode="shell" theme="idea">

```bash
$ kotlinc -script list_folders.kts -- -d <path_to_folder_to_inspect>
```

</div>

自 1.3.0 以来，Kotlin 对脚本定制提供了实验性支持，例如添加外部属性、<!--
-->提供静态或动态依赖关系等。自定义由所谓的 *脚本定义* <!--
-->带有适当支持代码的带注释的 kotlin 类定义。脚本文件扩展名用于选择适当的<!--
-->定义。

当适当的 jar 文件包含在<!--
-->编译类路径中时，会自动检测并正确应用正确编写的脚本定义。或可以使用编译器的 `-script-templates` 选项来手动定义：

<div class="sample" markdown="1" mode="shell" theme="idea">

```bash
$ kotlinc -script-templates org.example.CustomScriptDefinition -script custom.script1.kts
```

</div>

有关其他详细信息，请参阅[KEEP-75](https://github.com/Kotlin/KEEP/blob/master/proposals/scripting-support.md)。
                                                                                          
