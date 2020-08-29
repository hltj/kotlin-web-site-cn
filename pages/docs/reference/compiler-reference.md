---
type: doc
layout: reference
title: "Kotlin 编译器选项"
---

# Kotlin 编译器选项

每个 Kotlin 版本都包含支持目标的编译器：
用于[所支持平台](native-overview.html#目标平台)的 JVM、JavaScript 与 Native 二进制文件。

当单击 Kotlin 项目的 __Compile__ 或 __运行(Run)__ 按钮时，IDE 会使用这些编译器。

还可以按照[使用命令行编译器](/docs/tutorials/command-line.html)
教程中所述在命令行手动运行 Kotlin 编译器。例如：

<div class="sample" markdown="1" mode="shell" theme="idea">

```bash
$ kotlinc hello.kt -include-runtime -d hello.jar
```

</div>
 
## 编译器选项

Kotlin 编译器具有许多用于定制编译过程的选项。
此页面列出了针对不同目标的编译器选项，并提供了每个选项的描述。

有几种方法可以设置编译器选项及其值（_编译器参数_）：
- 在 IntelliJ IDEA 的 __设置(Settings) | 构建、执行、部署(Build, Execution, Deployment) | 编译器(Compilers) | Kotlin Compiler__ 窗口中，
在 __附加命令行参数(Additional command-line parameters)__ 文本框中输入编译器参数。
- 如果使用 Gradle，请在 Kotlin 编译任务的 `kotlinOptions` 属性中指定编译器参数。
详情请参见[使用 Gradle](using-gradle.html#编译器选项)。
- 如果使用 Maven，请在 Maven 插件节点的 `<configuration>` 元素中指定编译器参数。
详情请参见[使用 Maven](using-maven.html#指定编译器选项)。
- 如果运行命令行编译器，则将编译器参数直接添加到工具函数调用中，或将其写入 [argfile](#argfile)。

##  公共选项

以下选项对于所有 Kotlin 编译器都是通用的。

### `-version` 

显示编译器版本。
{:.details-group}

### `-nowarn`

禁止编译器在编译期间显示警告。
{:.details-group}

### `-Werror`

将所有警告转换为编译错误。
{:.details-group}

### `-verbose`

启用详细的日志记录输出，其中包括编译过程的详细信息。
{:.details-group}

### `-script`

运行 Kotlin 脚本文件。
使用此选项调用时，编译器将执行给定参数中的第一个 Kotlin 脚本文件（`*.kts`）。
{:.details-group}

### `-help` (`-h`)

显示用法信息并退出。仅显示标准选项。
要显示高级选项，请使用 `-X`。
{:.details-group}

### `-X`

显示有关高级选项的信息并退出。这些选项当前不稳定：
它们的名称与行为可能会更改，恕不另行通知。
{:.details-group}

### `-kotlin-home <path>`

指定用于发现运行时库的 Kotlin 编译器的自定义路径。
{:.details-group}
  
### `-P plugin:<pluginId>:<optionName>=<value>`

将选项传递给 Kotlin 编译器插件。
可用插件及其选项列在[编译器插件](compiler-plugins.html)中。
{:.details-group}
  
### `-language-version <version>`

提供与指定版本的 Kotlin 的源代码兼容性。
{:.details-group}

### `-api-version <version>`

仅允许使用 Kotlin 捆绑库指定版本中的声明。
{:.details-group}

### `-progressive`

为编译器启用[渐进模式](whatsnew13.html#渐进模式)。
{:.details-group}
在渐进模式下，针对不稳定代码的弃用与错误修复将立即生效，
而无需经历正常的迁移周期。
以渐进模式编写的代码是向后兼容的。
但是，以非渐进模式编写的代码可能会在渐进模式下导致编译错误。
{:.details-group}

{:#argfile}

### `@<argfile>`

从给定文件中读取编译器选项。这样的文件可以包含带有值与源文件路径的编译器选项。
选项与路径应由空格分隔。例如：
{:.details-group}

<div class="sample" markdown="1" mode="shell" theme="idea">

```
-include-runtime -d hello.jar
hello.kt
```

</div>

要传递包含空格的值，请用单引号（**'**）或双引号（**"**）引起来。
如果值中包含引号，请使用反斜杠（**\\**）对其进行转义。
{:.details-group}

<div class="sample" markdown="1" mode="shell" theme="idea">
     
```
-include-runtime -d 'My folder'
```
     
</div>

还可以传递多个参数文件，例如，将编译器选项与源文件分开。
{:.details-group}

<div class="sample" markdown="1" mode="shell" theme="idea">

```bash
$ kotlinc @compiler.options @classes
```

</div>

如果文件位于与当前目录不同的位置，请使用相对路径。
{:.details-group}

<div class="sample" markdown="1" mode="shell" theme="idea">

```bash
$ kotlinc @options/compiler.options hello.kt
```

</div>
    
## Kotlin/JVM 编译器选项

用于 JVM 的 Kotlin 编译器将 Kotlin 源文件编译为 Java 类文件。
用于 Kotlin 到 JVM 编译的命令行工具是 `kotlinc` 与 `kotlinc-jvm`。
也可以使用它们来执行 Kotlin 脚本文件。

除了[公共选项](#公共选项)外，Kotlin/JVM 编译器还具有以下列出的选项。

### `-classpath <path>` (`-cp <path>`)

在指定的路径中搜索类文件。用系统路径分隔符将类路径的各个元素分开（在Windows上是 **;** 在macOS/Linux上是 **:**）。
类路径可以包含文件和目录路径、ZIP 或 JAR 文件。
{:.details-group}

### `-d <path>`

将生成的类文件放置到指定位置。该位置可以是目录、ZIP 或 JAR 文件。
{:.details-group}

### `-include-runtime`

将 Kotlin 运行时包含在生成的 JAR 文件中。
使生成的归档文件可在任何启用 Java 的环境中运行。
{:.details-group}

### `-jdk-home <path>`

如果自定义 JDK 主目录与默认的 `JAVA_HOME` 不同，请使用它来将其包含在类路径中。
{:.details-group}

### `-jvm-target <version>`

指定生成的 JVM 字节码的目标版本。可能的值为 `1.6`、`1.8`、`9`、`10`、`11`、`12`、`13`。
默认值为 `1.6`。
{:.details-group}

### `-java-parameters`

为 Java 1.8 反射方法参数生成元数据。
{:.details-group}

### `-module-name <name>`

为生成的 `.kotlin_module` 文件设置自定义名称。
{:.details-group}
  
### `-no-jdk`

不要自动将 Java 运行时包含在类路径中。
{:.details-group}

### `-no-reflect`

不要自动将 Kotlin 反射（`kotlin-reflect.jar`）包含到类路径中。
{:.details-group}

### `-no-stdlib`

不要自动将 Kotlin/JVM 标准库（`kotlin-stdlib.jar`）与
Kotlin 反射（`kotlin-reflect.jar`）包含到类路径中。
{:.details-group}
  
### `-script-templates <classnames[,]>`

脚本定义模板类。使用完全限定的类名，并用逗号（**,**）分隔。
{:.details-group}


## Kotlin/JS 编译器选项

JS 的 Kotlin 编译器将 Kotlin 源文件编译为 JavaScript 代码。
Kotlin 到 JS 编译的命令行工具是 `kotlinc-js`。

除了[公共选项](#公共选项)外，Kotlin/JS 编译器还具有以下列出的选项。

### `-libraries <path>`

具有 `.meta.js` 与 `.kjsm` 文件的 Kotlin 库的路径，由系统路径分隔符分隔。
{:.details-group}

### `-main {call|noCall}`

定义是否应在执行时调用 `main` 函数。
{:.details-group}

### `-meta-info`

使用元数据生成 `.meta.js` 与 `.kjsm` 文件。 创建 JS 库时使用此选项。
{:.details-group}

### `-module-kind {umd|commonjs|amd|plain}`

编译器生成的 JS 模块类型：
{:.details-group}
- `umd` ——[通用模块定义](https://github.com/umdjs/umd)模块
- `commonjs` ——[CommonJS](http://www.commonjs.org/) 模块
- `amd` ——[异步模块定义](https://en.wikipedia.org/wiki/Asynchronous_module_definition)模块
- `plain` ——普通的 JS 模块
    
要了解有关不同类型的 JS 模块及其之间区别的更多信息，
请参见[这篇文章](https://www.davidbcalhoun.com/2014/what-is-amd-commonjs-and-umd/)。
{:.details-group}

### `-no-stdlib`

不要自动将默认的 Kotlin/JS 标准库包含在编译依赖项中。
{:.details-group}

### `-output <filepath>`

设置编译结果的目标文件。该值必须是包含其名称的 `.js` 文件的路径。
{:.details-group}

### `-output-postfix <filepath>`

将指定文件的内容添加到输出文件的末尾。
{:.details-group}

### `-output-prefix <filepath>`

将指定文件的内容添加到输出文件的开头。
{:.details-group}

### `-source-map`

生成源代码映射。
{:.details-group}

### `-source-map-base-dirs <path>`

使用指定的路径作为基本目录。基本目录用于计算源代码映射中的相对路径。
{:.details-group}

### `-source-map-embed-sources {always|never|inlining}`

将源代码文件嵌入到源代码映射中。
{:.details-group}

### `-source-map-prefix`

将指定的前缀添加到源代码映射中的路径。
{:.details-group}


## Kotlin/Native 编译器选项

Kotlin/Native 编译器将 Kotlin 源代码文件编译为用于[所支持平台](native-overview.html#目标平台)的 Native 二进制文件。
Kotlin/Native 编译的命令行工具是 `kotlinc-native`。

除了[公共选项](#公共选项)外，Kotlin/Native 编译器还具有以下列出的选项。


### `-enable-assertions` (`-ea`)

在生成的代码中启用运行时断言。
{:.details-group}
    
### `-g`

启用输出调试信息。
{:.details-group}
    
### `-generate-test-runner` (`-tr`)

生成一个用于运行项目中的单元测试的应用程序。
{:.details-group}    
### `-generate-worker-test-runner` (`-trw`)

生成一个用于在 [Worker 线程](native/concurrency.html#worker)中运行单元测试的应用程序。
{:.details-group}
    
### `-generate-no-exit-test-runner` (`-trn`)

生成一个用于运行单元测试的应用程序，而无需显式退出进程。
{:.details-group}
    
### `-include-binary <path>` (`-ib <path>`)

在生成的 klib 文件中打包外部二进制文件。
{:.details-group}
    
### `-library <path>` (`-l <path>`)

与库链接。要了解有关在 Kotlin/native 项目中使用库的信息，
请参见 [Kotlin/Native 库](native/libraries.html)。
{:.details-group}

### `-library-version <version>` (`-lv`)

设置库版本。
{:.details-group}
    
### `-list-targets`

列出可用的硬件目标。
{:.details-group}

### `-manifest <path>`

提供清单附加文件。
{:.details-group}

### `-module-name <name>`

指定编译模块的名称。
此选项还可用于为导出到 Objective-C 的声明指定名称前缀：
[如何为 Kotlin 框架指定自定义的 Objective-C 前缀/名称？](native/faq.html#q-how-do-i-specify-a-custom-objective-c-prefixname-for-my-kotlin-framework)
{:.details-group}

### `-native-library <path>`(`-nl <path>`)

包含 Native Bitcode 库。
{:.details-group}

### `-no-default-libs`

禁止将用户代码与编译器一起分发的[默认平台库](native/platform_libs.html)链接。
{:.details-group}
    
### `-nomain`

假定要由外部库提供的 `main` 入口点。
{:.details-group}

### `-nopack`

不要将库打包到 klib 文件中。
{:.details-group}

### `-linker-option`

在二进制构建过程中，将参数传递给链接器。这可用于链接到某些 Native 库。
{:.details-group}

### `-linker-options <args>`

在二进制构建过程中，将多个参数传递给链接器。用空格分隔参数。
{:.details-group}

### `-nostdlib`

不要与标准库链接。
{:.details-group}

### `-opt`

启用编译优化。
{:.details-group}

### `-output <name>` (`-o <name>`)

设置输出文件的名称。
{:.details-group}

### `-entry <name>` (`-e <name>`)

指定限定的入口点名称。
{:.details-group}

### `-produce <output>` (`-p`)

指定输出文件的种类：
{:.details-group}
- `program`
- `static`
- `dynamic`
- `framework`
- `library`
- `bitcode`

### `-repo <path>` (`-r <path>`)

库搜索路径。有关更多信息，请参见[库搜索顺序](native/libraries.html#库搜索顺序)。
{:.details-group}

### `-target <target>`

设置硬件目标。要查看可用目标的列表，请使用 [`-list-targets`](#-list-targets) 选项。
{:.details-group}

  
